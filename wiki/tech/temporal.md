---
type: tech
tags: [temporal, workflows, orchestration, distributed-systems, go]
created: 2026-05-04
updated: 2026-05-04
sources: []
---

# Temporal

Temporal is a durable execution platform for orchestrating long-running, fault-tolerant business processes. A Temporal Application is built from three primitives — Workflows, Activities, and Workers — written in ordinary code via language SDKs (Go, Java, TypeScript, Python, .NET, PHP, Ruby).

## Core primitives (as of 2026-05-04)

| Primitive | Role |
|-----------|------|
| **Workflow** | Defines the overall flow; orchestrates a sequence of steps and Activity calls. Must be deterministic for replay. |
| **Activity** | A method/function encapsulating failure-prone business logic (calling a service, transcoding media, sending email). The platform automatically retries on failure. Performs a single, well-defined action. |
| **Worker** | Process that executes Workflow and Activity code. The Temporal service drives orchestration; the Worker pulls work items and executes them. |

> One-line model: Workflows orchestrate, Activities do the work that can fail, Workers run both.

## Go SDK surface area

### Workflows

The `Workflows` section of the Go SDK docs covers:

- **Workflow basics** — defining and registering workflow functions
- **Child Workflows** — workflows scheduled by other workflows
- **Continue-As-New** — restart a workflow with new input while preserving identity (used for long-running monitor patterns)
- **Cancellation** — cooperative cancellation propagation
- **Timeouts** — workflow-level timeouts
- **Message passing** — Signals (push input into a running workflow) and Queries (read state out)
- **Selectors** — wait on the first of multiple async events to fire
- **Side effects** — escape hatch for non-deterministic reads inside a workflow
- **Schedules** — cron-like recurring workflow triggers
- **Timers** — durable timers; the workflow unloads from memory while waiting
- **Dynamic Workflow** — register handlers by name at runtime
- **Versioning** — first-class support for evolving workflow code without breaking in-flight executions

### Activities

- **Activity basics** — defining and registering activity functions
- **Activity execution** — invocation semantics, retries
- **Standalone Activities** — Activities that can be invoked outside of a Workflow context
- **Timeouts** — activity-level timeouts (start-to-close, schedule-to-close, heartbeat)
- **Asynchronous Activity completion** — completing an activity from a different process via task token
- **Dynamic Activity** — register handlers by name at runtime
- **Benign exceptions** — distinguishing expected business errors from infrastructure failures so retries don't fire on intended outcomes

## Comparison to [[wiki/tech/dapr-workflows]]

Dapr Workflows is conceptually descended from Temporal's model — same three primitives, same determinism rules, same activity-retry-with-durable-timer pattern. Differences worth noting:

| Aspect | Temporal | Dapr Workflows |
|--------|----------|----------------|
| **Deployment model** | Standalone Temporal service (`temporal server`) + Worker processes | Embedded in the Dapr sidecar (`durabletask-go` lib) |
| **State store** | Cassandra, PostgreSQL, MySQL, SQLite | Whatever Dapr actor state store is configured (PostgreSQL, MySQL, SQL Server, SQLite, Oracle, CockroachDB, MongoDB, Redis) |
| **Versioning** | First-class workflow versioning topic | Not yet a primary docs topic; determinism rules acknowledge the problem |
| **Standalone Activities** | Supported (Activities outside a Workflow context) | Not a documented pattern |
| **Message passing** | Signals + Queries | External events (`WaitForExternalEvent`); no built-in synchronous Query primitive |
| **Selectors** | First-class primitive for "first-of" composition | Not exposed as a named primitive |
| **Multi-app/cluster** | Built-in via Workflow ID + Task Queue routing | `WithActivityAppID` / `WithChildWorkflowAppID` (v1.16+, same namespace + actor state store required) |
| **CLI** | `temporal` CLI for cluster + workflow operations | `dapr workflow` and `dapr scheduler` for workflow + reminder management |

The bidirectional message passing primitives (Signals + Queries) and explicit Versioning chapter are the most notable Temporal-specific concepts not directly mirrored in Dapr Workflows as of 2026-05-04.

## Significance

Temporal is the de-facto reference implementation of the durable-execution pattern; multiple ecosystems (Dapr Workflows, AWS Step Functions, Cadence — Temporal's predecessor) draw from the same model. For Go-heavy distributed systems work, Temporal vs Dapr Workflows is a real architectural choice: Temporal pushes you toward a dedicated workflow cluster; Dapr keeps the workflow engine inside the sidecar adjacent to the rest of your building blocks (state, pub/sub, actors).

## Open questions

- How does Temporal's Versioning API compare to Dapr's (currently informal) approach?
- What does "Standalone Activities" mean operationally — Activity-only Workers without Workflow registration?
- How is "Benign exceptions" implemented — error type classification at the SDK boundary?

These would be answered by ingesting the Temporal subpages (each link in the Workflows / Activities indexes points to a separate doc).

## See also

- [[wiki/tech/dapr-workflows]] — descendant pattern, embedded in Dapr sidecar
- [[wiki/tech/dapr-workflow-patterns]] — task chaining, fan-out/fan-in, monitor, external system interaction, compensation/saga (the same patterns apply to Temporal)
- [[wiki/tech/awesome-go]] — Go ecosystem reference

**Raw sources:**

- [[raw/ingested/articles/Core application - Temporal feature  Temporal Platform Documentation]]
- [[raw/ingested/articles/Workflows - Go SDK  Temporal Platform Documentation]]
- [[raw/ingested/articles/Activities - Go SDK  Temporal Platform Documentation]]
