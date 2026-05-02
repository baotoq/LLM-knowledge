---
type: tech
tags: [dapr, workflows, orchestration, microservices, distributed-systems]
created: 2026-05-02
updated: 2026-05-03
sources: []
---

# Dapr Workflows

Dapr's workflow building block enables orchestrating long-running, stateful processes across microservices. Built on `durabletask-go` inside the Dapr sidecar, workflows are durable (survive crashes), fault-tolerant (automatic replay/retry), and expressed as ordinary code in any supported SDK language.

## Go SDK pattern (current — 2026-05-03)

Uses two packages: `github.com/dapr/go-sdk/client` and `github.com/dapr/durabletask-go/workflow`.

```go
import (
    "github.com/dapr/durabletask-go/workflow"
    "github.com/dapr/go-sdk/client"
)

// 1. Build a registry and register workflows + activities
r := workflow.NewRegistry()
r.AddWorkflow(OrderProcessingWorkflow)
r.AddActivity(NotifyActivity)
r.AddActivity(VerifyInventoryActivity)

// 2. Create a workflow client and start the worker
wfClient, _ := client.NewWorkflowClient()
wfClient.StartWorker(ctx, r)

// 3. Schedule a workflow instance
id, _ := wfClient.ScheduleWorkflow(ctx, "OrderProcessingWorkflow",
    workflow.WithInput(payload),
    workflow.WithInstanceID("order-"+timestamp),
)

// 4. Wait for completion and fetch result
wfClient.WaitForWorkflowCompletion(ctx, id)
meta, _ := wfClient.FetchWorkflowMetadata(ctx, id, workflow.WithFetchPayloads(true))
```

> [!warning] Contradicts earlier pattern (from `dapr-go-client.md` ingest)
> Earlier docs showed `workflow.NewWorker()` + `client.ScheduleNewWorkflow()` + `client.WaitForWorkflowCompletion()`. The quickstart (2026-05-03) uses `workflow.NewRegistry()` + `client.NewWorkflowClient()` + `wfClient.StartWorker()`. The registry-based approach appears to be the current recommended pattern.

## Workflow function structure

```go
func OrderProcessingWorkflow(ctx *workflow.WorkflowContext) (any, error) {
    var input OrderPayload
    ctx.GetInput(&input)

    // Call an activity and await its result
    var result InventoryResult
    ctx.CallActivity(VerifyInventoryActivity,
        workflow.WithActivityInput(request),
    ).Await(&result)

    // Durable timer
    ctx.CreateTimer(time.Second * 30).Await(nil)

    // Wait for an external event (e.g. human approval)
    if input.TotalCost > 5000 {
        ctx.WaitForExternalEvent("manager_approval", 200*time.Second).Await(nil)
    }

    return OrderResult{Processed: true}, nil
}

func VerifyInventoryActivity(ctx workflow.ActivityContext) (any, error) {
    var input InventoryRequest
    ctx.GetInput(&input)
    // ... do work, call Dapr state store, external APIs, etc.
    return InventoryResult{Success: true}, nil
}
```

## Key concepts

- **Workflow** — orchestrator function (`*workflow.WorkflowContext`); defines the sequence of activities and decisions. Must be deterministic.
- **Activity** — individual unit of work (`workflow.ActivityContext`); can call external services, databases, APIs. All side effects belong here, not in the workflow function. Guaranteed **at-least-once** execution.
- **Registry** — collects workflow and activity function registrations before starting the worker.
- **WorkflowClient** — separate from the standard Dapr client; used to schedule, wait, query, and manage workflow instances.
- **Child workflow** — workflow scheduled by another workflow; has its own instance ID, history, and status. Terminating parent terminates all children.
- **Durable timer** — `ctx.CreateTimer(duration)` — backed by actor reminders; workflows unload from memory while waiting. No limit on duration.

## Determinism rules (critical)

The replay mechanism requires workflow functions to be **deterministic** — given the same history, the function must produce the same sequence of actions. Breaking determinism causes runtime errors on replay.

### Rules

| Constraint | Wrong | Right |
|---|---|---|
| Current time | `time.Now()` | `ctx.CurrentUTCDateTime()` |
| External HTTP calls | `http.Get(url)` in workflow | Delegate to activity |
| Environment variables | `os.Getenv("KEY")` in workflow | Pass via workflow input |
| Goroutines | `go func() { ctx.CallActivity... }()` | Call activities inline, use fan-out pattern |
| Random values | `rand.Intn(100)` in workflow | Delegate to activity |

### Correct time usage in Go

```go
// DON'T DO THIS in a workflow function
currentTime := time.Now()

// Do this
currentTime := ctx.CurrentUTCDateTime()
```

### Correct concurrency in Go

```go
// DON'T DO THIS - goroutine in workflow
go func() {
    err := ctx.CallActivity(DoSomething).Await(nil)
}()

// Do this - direct call on the workflow goroutine
task := ctx.CallActivity(DoSomething)
task.Await(nil)
```

> [!warning] I/O belongs in activities
> Because of the replay model, workflows that execute I/O directly (network calls, env vars, filesystem) will re-execute that I/O on every replay, causing incorrect behavior and potential side effects.

## Retry policy

Retry policies for activities and child workflows are configured in code (separate from Dapr Resiliency YAML policies). They are durable — state survives app restarts.

```go
ctx.CallActivity(FailActivity, workflow.WithActivityRetryPolicy(&workflow.RetryPolicy{
    MaxAttempts:          3,
    InitialRetryInterval: 100 * time.Millisecond,
    BackoffCoefficient:   2,
    MaxRetryInterval:     1 * time.Second,
})).Await(nil)
```

| Parameter | Description |
|---|---|
| `MaxAttempts` | Maximum retry attempts (0 = no attempts) |
| `InitialRetryInterval` | Wait before first retry |
| `BackoffCoefficient` | Multiplier on each subsequent interval |
| `MaxRetryInterval` | Cap on inter-retry wait |
| `RetryTimeout` | Global timeout across all retry attempts |

Retries are internally implemented using durable timers, so the workflow unloads from memory between attempts.

## External event handling

Workflows can pause and wait for an external signal:

```go
// In the workflow function — suspend until event arrives or timeout
var approval Approval
if err := ctx.WaitForExternalEvent("approval_received", time.Hour*24).Await(&approval); err != nil {
    return "cancelled/timeout", err
}
```

```bash
# From outside — raise the event via CLI
dapr workflow raise-event <instance-id>/approval_received \
  --app-id order-processor \
  --input '{"approver": "Jane Doe"}'
```

External events are delivered in FIFO order if multiple events of the same name are pending. If an event arrives before the workflow calls `WaitForExternalEvent`, it is buffered and consumed immediately when requested.

## Multi-app workflows (Dapr v1.16+, 2026-05-03)

Activities and child workflows can be executed on a different app ID by passing the target app ID parameter:

```go
// Activity on a different app
err := ctx.CallActivity("ActivityA",
    workflow.WithActivityInput("my-input"),
    workflow.WithActivityAppID("App2"),
).Await(&output)

// Child workflow on a different app
err := ctx.CallChildWorkflow("Workflow2",
    workflow.WithChildWorkflowInput("my-input"),
    workflow.WithChildWorkflowAppID("App2"),
).Await(&output)
```

Restrictions:
- All app IDs must be in the **same namespace**
- All app IDs must use the **same actor state store**
- The target app must have the activity/child workflow registered; otherwise the parent retries indefinitely

> [!warning] Durable Activity Results (v1.17)
> In v1.17, enable the `WorkflowsRemoteActivityReminder` feature gate to prevent activity result loss when the owning workflow app is temporarily offline. Disabled by default for backwards compatibility; will become default in a future release.

## Workflow management CLI

```bash
# Start a workflow instance
dapr workflow run OrderProcessingWorkflow \
  --app-id orderprocessing \
  --input '{"orderId": "12345"}' \
  --instance-id order-12345

# Schedule to start at a future time
dapr workflow run OrderProcessingWorkflow \
  --app-id orderprocessing \
  --start-time "2024-12-25T10:00:00Z"

# List all instances (filter by status, name, age)
dapr workflow list --app-id order-processor -o wide
dapr workflow list --filter-status RUNNING
dapr workflow list --filter-name OrderProcessingWorkflow
dapr workflow list --filter-max-age 24h

# View detailed execution history
dapr workflow history <instance-id> --app-id order-processor
dapr workflow history <instance-id> --app-id order-processor --output json

# Suspend / resume
dapr workflow suspend <instance-id> --app-id order-processor --reason "..."
dapr workflow resume  <instance-id> --app-id order-processor --reason "..."

# Terminate a running workflow
dapr workflow terminate <instance-id> --app-id order-processor

# Raise an external event
dapr workflow raise-event <instance-id>/<event-name> --app-id order-processor --input '{}'

# Re-run from the beginning or from a specific event
dapr workflow rerun <instance-id>
dapr workflow rerun <instance-id> --event-id 5
dapr workflow rerun <instance-id> --new-instance-id order-12345-retry

# Purge completed workflows (requires workflow client to be running)
dapr workflow purge <instance-id> --app-id order-processor
dapr workflow purge --all-older-than 720h
dapr workflow purge --all-older-than 720h --all-filter-status FAILED
dapr workflow purge --app-id orderprocessing --all

# Force purge without a running client (DANGEROUS - corrupts state if instances still running)
dapr workflow purge <instance-id> --force
```

**Kubernetes:** add `--kubernetes` / `-k` flag and `--namespace` to any command.

> [!warning] Purge requires a running workflow client
> `dapr workflow purge` fails with a `FailedPrecondition` error if the workflow client is not currently running. Use `--force` only when certain no instances are active — it will corrupt the state machine otherwise.

## Scheduler reminder management

Workflow timers are stored as Scheduler reminders and can be inspected/managed independently:

```bash
# List all workflow reminders
dapr scheduler list --filter workflow

# Get details for a specific reminder
dapr scheduler get workflow/my-app/instance1/timer-0-ABC123 -o yaml

# Delete a single reminder
dapr scheduler delete workflow/my-app/instance1/timer-0-ABC123

# Delete all reminders for a workflow app
dapr scheduler delete-all workflow/my-app

# Delete all reminders for a specific instance
dapr scheduler delete-all workflow/my-app/instance1

# Backup and restore reminders
dapr scheduler export -o workflow-reminders-backup.bin
dapr scheduler import -f workflow-reminders-backup.bin
```

> [!warning] Purge also deletes Scheduler reminders
> `dapr workflow purge` deletes all associated Scheduler reminders as part of cleanup.

## Operational configuration

### Concurrency limits

Applied per sidecar. With N replicas, effective limit = N × configured value.

```yaml
apiVersion: dapr.io/v1alpha1
kind: Configuration
metadata:
  name: appconfig
spec:
  workflow:
    maxConcurrentWorkflowInvocations: 100   # Default: infinite
    maxConcurrentActivityInvocations: 1000  # Default: infinite
```

### History retention policy

Controls how long terminal workflow state is retained before deletion. Policies only apply to workflows that **newly reach** a terminal state after the policy is applied — not retroactively.

```yaml
spec:
  workflow:
    stateRetentionPolicy:
      anyTerminal: "360h"   # fallback for any terminal state not explicitly set
      completed: "1m"       # short retention for healthy completed workflows
      failed: "720h"        # retain failed for post-mortem
      terminated: "360h"
```

To retroactively purge existing terminal workflows:
```bash
dapr workflow purge --app-id <app-id> --all-older-than <duration>
dapr workflow purge --app-id <app-id> --all-older-than <duration> --all-filter-status COMPLETED
```

## Architecture internals

### gRPC streaming (no inbound ports)

The workflow app uses a **pull model**: it opens a gRPC stream to the sidecar and receives work items from it. This means the application doesn't need to open any inbound ports for workflow execution — unlike actors which require an `app-port`.

The `durabletask-go` framework is embedded directly in the Dapr sidecar. The sidecar drives orchestration while the SDK handles the gRPC protocol transparently.

### Actor types

Two internal actor types are registered per workflow app:
- `dapr.internal.{namespace}.{appID}.workflow` — one per workflow instance; manages state and orchestration
- `dapr.internal.{namespace}.{appID}.activity` — one per activity invocation; short-lived, no state stored

These are invisible to application code and distributed across the cluster by the actor placement service. See [[wiki/tech/dapr-actors]] for how the virtual actor model and placement service work.

### State store keys

Each workflow actor stores state with these keys in the actor state store:

| Key | Description |
|---|---|
| `inbox-NNNNNN` | FIFO queue of pending messages (workflow creation, activity completions, etc.); keys removed once consumed |
| `history-NNNNNN` | Append-only execution history; one key per history event |
| `customStatus` | User-defined status value |
| `metadata` | JSON blob with inbox/history lengths and generation counter |

> [!warning] Workflow state persists after completion
> Workflow actor state remains in the state store even after the workflow completes. Large numbers of workflows will accumulate unbounded storage unless purged. Use retention policies or `dapr workflow purge` to manage this.

### Supported state stores

PostgreSQL, MySQL, SQL Server, SQLite, Oracle Database, CockroachDB, MongoDB, Redis.

### Throughput optimization

By default, `ScheduleWorkflow` blocks until the workflow is fully started before returning. To increase scheduling throughput, pass a start time of "now":

```go
wfClient.ScheduleWorkflow(ctx, "MyCoolWorkflow",
    workflow.WithStartTime(time.Now()),
)
```

This returns immediately without waiting for the workflow to start.

## Production gotcha: Redis state store

Redis (the default state store in Dapr quickstarts) does not support transaction rollbacks. **Do not use Redis as an actor state store in production workflows.** Use a transactional-safe store (e.g. PostgreSQL, Azure Cosmos DB) for production deployments (as of 2026-05-03).

## See also

- [[wiki/tech/dapr-actors]] — actor building block that workflows are built on
- [[wiki/tech/dapr-workflow-patterns]] — task chaining, fan-out/fan-in, monitor, external system interaction, compensation/saga
- [[wiki/tech/dapr-go-sdk]] — Workflows are one of the Client SDK building blocks
- [[wiki/tech/dapr-pubsub]] — another Dapr building block; pub/sub for event-driven messaging
- [[wiki/tech/dapr-jobs]] — scheduled job execution, also via the Client SDK
**Raw sources:**
- [[raw/ingested/articles/Quickstart Workflow]]
- [[raw/ingested/articles/How to Author a workflow]]
- [[raw/ingested/articles/How to Manage workflows]]
- [[raw/ingested/articles/Features and concepts]]
- [[raw/ingested/articles/Workflow architecture]]
- [[raw/ingested/articles/Workflow overview]]
- [[raw/ingested/articles/Multi Application Workflows]]
- [[raw/ingested/articles/Workflow Execution Concurrency]]
- [[raw/ingested/articles/History Retention Policy]]
