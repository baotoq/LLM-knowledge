---
type: tech
tags: [dapr, workflows, orchestration, microservices, distributed-systems]
created: 2026-05-02
updated: 2026-05-02
sources: []
---

# Dapr Workflows

Dapr's workflow building block enables orchestrating long-running, stateful processes across microservices. The Go SDK uses a **worker pattern**: register workflow and activity functions, start the worker, then schedule and monitor executions via a workflow client.

## Worker pattern (Go SDK)

```go
// 1. Register workflow and activity functions
w := workflow.NewWorker()
w.RegisterWorkflow(myWorkflow)
w.RegisterActivity(myActivity)

// 2. Start the worker
w.Start()

// 3. Schedule and wait via client
id, _ := client.ScheduleNewWorkflow(ctx, "myWorkflow", ...)
result, _ := client.WaitForWorkflowCompletion(ctx, id, ...)
```

## Key concepts

- **Workflow** — orchestrator function; defines the sequence of activities and decisions
- **Activity** — individual unit of work; can call external services, databases, APIs
- The worker stays running and polls Dapr for workflow tasks to execute

## See also

- [[wiki/tech/dapr-go-sdk]] — Workflows are one of the Client SDK building blocks
- [[wiki/tech/dapr-pubsub]] — another Dapr building block; pub/sub for event-driven messaging
- [[wiki/tech/dapr-jobs]] — scheduled job execution, also via the Client SDK
- [Dapr workflows overview](https://docs.dapr.io/developing-applications/building-blocks/workflow/)
- Raw: `raw/ingested/articles/dapr-go-client.md`
