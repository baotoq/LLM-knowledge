---
type: tech
tags: [dapr, workflows, patterns, orchestration, distributed-systems]
created: 2026-05-03
updated: 2026-05-03
sources: []
---

# Dapr Workflow Patterns

Five core patterns for Dapr Workflow, covering the major coordination problems in distributed microservice architectures. Go examples are shown throughout.

## Task chaining

Execute activities in sequence, passing output of one step as input to the next. The simplest pattern — the runtime handles durability, retries, and crash recovery automatically.

```go
func TaskChainWorkflow(ctx *workflow.WorkflowContext) (any, error) {
    var input int
    ctx.GetInput(&input)

    var result1, result2, result3 int
    if err := ctx.CallActivity(Step1, workflow.ActivityInput(input)).Await(&result1); err != nil {
        return nil, err
    }
    if err := ctx.CallActivity(Step2, workflow.ActivityInput(result1)).Await(&result2); err != nil {
        return nil, err
    }
    if err := ctx.CallActivity(Step3, workflow.ActivityInput(result2)).Await(&result3); err != nil {
        return nil, err
    }
    return []int{result1, result2, result3}, nil
}
```

If the workflow crashes mid-chain, the runtime automatically resumes from the last completed step — no manual checkpointing needed.

## Fan-out / fan-in

Dispatch N parallel tasks, wait for all to complete, then aggregate results. The number of parallel tasks can be dynamic (determined at runtime).

```go
func BatchProcessingWorkflow(ctx *workflow.WorkflowContext) (any, error) {
    var batchSize int
    ctx.GetInput(&batchSize)

    // Get the work batch (as an activity — no direct I/O in workflow)
    var workBatch []int
    ctx.CallActivity(GetWorkBatch, workflow.ActivityInput(batchSize)).Await(&workBatch)

    // Fan out: schedule all tasks without awaiting
    parallelTasks := workflow.NewTaskSlice(len(workBatch))
    for i, workItem := range workBatch {
        parallelTasks[i] = ctx.CallActivity(ProcessWorkItem, workflow.ActivityInput(workItem))
    }

    // Fan in: collect all results
    var total int
    for _, task := range parallelTasks {
        var output int
        if err := task.Await(&output); err == nil {
            total += output
        }
    }

    ctx.CallActivity(ProcessResults, workflow.ActivityInput(total)).Await(nil)
    return total, nil
}
```

**Key property:** If the app crashes after 40 of 100 tasks complete, the runtime restarts and reschedules only the remaining 60 — the completed tasks are not re-executed.

## Monitor (eternal workflow)

A recurring polling loop that adjusts its sleep interval based on observed state. Avoids infinite loops by using `ContinueAsNew`, which truncates history and restarts the workflow with fresh input — essential for long-running monitors.

```go
type JobStatus struct {
    JobID     string `json:"job_id"`
    IsHealthy bool   `json:"is_healthy"`
}

func StatusMonitorWorkflow(ctx *workflow.WorkflowContext) (any, error) {
    var job JobStatus
    ctx.GetInput(&job)

    var status string
    ctx.CallActivity(CheckStatus, workflow.ActivityInput(job)).Await(&status)

    var sleepInterval time.Duration
    if status == "healthy" {
        job.IsHealthy = true
        sleepInterval = time.Minute * 60 // poll less frequently when healthy
    } else {
        if job.IsHealthy {
            job.IsHealthy = false
            ctx.CallActivity(SendAlert, workflow.ActivityInput(
                fmt.Sprintf("Job '%s' is unhealthy!", job.JobID),
            )).Await(nil)
        }
        sleepInterval = time.Minute * 5 // poll more frequently when unhealthy
    }

    ctx.CreateTimer(sleepInterval).Await(nil)

    // Restart with updated state — truncates history, keeps history size bounded
    ctx.ContinueAsNew(job, false)
    return "", nil
}
```

The second argument to `ContinueAsNew` controls whether pending external events are carried over to the new instance.

> [!warning] Never use `for {}` in workflow functions
> An infinite loop accumulates unbounded history and eventually causes memory/storage exhaustion. Always use `ContinueAsNew` for eternal workflows.

## External system interaction

Pause the workflow and wait for a signal from an external system — most commonly human approval, a payment confirmation, or an async callback. The workflow releases resources while waiting; there is no polling.

```go
type Order struct {
    Cost    float64 `json:"cost"`
    Product string  `json:"product"`
}
type Approval struct {
    Approver string `json:"approver"`
}

func PurchaseOrderWorkflow(ctx *workflow.WorkflowContext) (any, error) {
    var order Order
    ctx.GetInput(&order)

    // Auto-approve small orders
    if order.Cost < 1000 {
        return "Auto-approved", nil
    }

    // Send approval request (via activity — side effects belong in activities)
    ctx.CallActivity(SendApprovalRequest, workflow.ActivityInput(order)).Await(nil)

    // Pause until approval received or 24-hour timeout
    var approval Approval
    if err := ctx.WaitForExternalEvent("approval_received", time.Hour*24).Await(&approval); err != nil {
        // Timeout — cancel the order
        return "error/cancelled", err
    }

    ctx.CallActivity(PlaceOrder, workflow.ActivityInput(order)).Await(nil)
    return fmt.Sprintf("Approved by %s", approval.Approver), nil
}
```

Raise the event from outside (another service, a CLI command, or a pub/sub listener):

```go
// Via Dapr client (Go)
daprClient.RaiseEventWorkflow(ctx, &client.RaiseEventWorkflowRequest{
    InstanceID:        "instance_id",
    WorkflowComponent: "dapr",
    EventName:         "approval_received",
    EventData:         Approval{Approver: "Jane Doe"},
})
```

```bash
# Via CLI
dapr workflow raise-event <instance-id>/approval_received \
  --app-id order-processor \
  --input '{"approver": "Jane Doe"}'
```

**Multiple events of the same name** are dispatched FIFO. An event that arrives before `WaitForExternalEvent` is called is buffered and consumed immediately when the workflow requests it.

## Compensation (saga)

Roll back completed steps when a later step fails. The workflow maintains a list of completed steps and executes their compensating actions in reverse order on failure.

```go
func OrderProcessingWorkflow(ctx *workflow.WorkflowContext) (any, error) {
    var orderId string
    ctx.GetInput(&orderId)

    // Track which compensations to run on failure
    var compensations []string

    // Step 1: Reserve inventory
    var reservationId string
    if err := ctx.CallActivity(ReserveInventory, workflow.ActivityInput(orderId)).Await(&reservationId); err != nil {
        return nil, err
    }
    compensations = append(compensations, "ReleaseInventory")

    // Step 2: Process payment
    var paymentId string
    if err := ctx.CallActivity(ProcessPayment, workflow.ActivityInput(orderId)).Await(&paymentId); err != nil {
        runCompensations(ctx, compensations, orderId)
        return nil, err
    }
    compensations = append(compensations, "RefundPayment")

    // Step 3: Ship order
    var shipmentId string
    if err := ctx.CallActivity(ShipOrder, workflow.ActivityInput(orderId)).Await(&shipmentId); err != nil {
        runCompensations(ctx, compensations, orderId)
        return nil, err
    }

    return fmt.Sprintf("Order %s processed: reservation=%s payment=%s shipment=%s",
        orderId, reservationId, paymentId, shipmentId), nil
}

func runCompensations(ctx *workflow.WorkflowContext, compensations []string, orderId string) {
    // Execute in reverse order
    for i := len(compensations) - 1; i >= 0; i-- {
        switch compensations[i] {
        case "RefundPayment":
            ctx.CallActivity(RefundPayment, workflow.ActivityInput(orderId)).Await(nil)
        case "ReleaseInventory":
            ctx.CallActivity(ReleaseInventory, workflow.ActivityInput(orderId)).Await(nil)
        }
    }
}
```

Key properties:
- **No distributed transactions** — compensation is the practical alternative for microservices with independent data stores
- **Compensation failures** — handle separately; a failed compensation may need its own retry logic
- **Activity idempotency** — since activities are at-least-once, compensation activities should also be idempotent

## Async HTTP APIs

Workflows implement the [Async Request-Reply pattern](https://learn.microsoft.com/azure/architecture/patterns/async-request-reply) out of the box via the HTTP API. A client starts a workflow, gets back an instance ID, and polls for status — no additional code or state management required.

```bash
# Start workflow (returns instance ID immediately)
curl -X POST "http://localhost:3500/v1.0/workflows/dapr/OrderProcessingWorkflow/start?instanceID=12345678" \
  -d '{"Name":"Paperclips","Quantity":1,"TotalCost":9.95}'
# → {"instanceID":"12345678"}

# Poll for status
curl "http://localhost:3500/v1.0/workflows/dapr/12345678"
# → {"runtimeStatus": "RUNNING", ...}
# → {"runtimeStatus": "COMPLETED", "properties": {"dapr.workflow.output": "..."}}
```

The client polls until `runtimeStatus` is `COMPLETED`, `FAILED`, or `TERMINATED`.

## See also

- [[wiki/tech/dapr-workflows]] — SDK setup, determinism rules, CLI management, operational config
**Raw sources:**
- [[raw/ingested/articles/Workflow patterns]]
