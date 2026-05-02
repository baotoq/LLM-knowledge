---
type: tech
tags: [dapr, actors, distributed-systems, virtual-actors, microservices]
created: 2026-05-03
updated: 2026-05-03
sources: []
---

# Dapr Actors

Dapr's actor building block implements the **Virtual Actor pattern** (from Microsoft Orleans): actors are stateful, single-threaded computational units activated on demand and garbage-collected when idle, with state persisting independently in a backing state store.

## The Virtual Actor model

Unlike traditional actor systems (Erlang, Akka), Dapr actors are *virtual* — they don't need to be explicitly created or destroyed. The runtime:

1. Automatically activates an actor on the first request for a given actor ID
2. Garbage-collects the in-memory object when the actor is idle
3. Persists state to the configured state store independently of the object's lifetime

An actor's state survives deactivation. On next activation the actor resumes with its previous state.

## Actor types and IDs

Every actor is an instance of an **actor type** (analogous to a class) identified by an **actor ID** (any string). Multiple actors of the same type can coexist across a cluster; each is uniquely addressable by `{type}/{id}`.

```csharp
// type: "SmokeDetectorActor", id: "1"
ActorProxy.Create<ISmartDevice>(new ActorId("1"), "SmokeDetectorActor")
```

Dapr generates a random ID if none is provided.

## Turn-based concurrency

The actor runtime processes one method call at a time per actor instance — no locks or synchronization primitives needed in application code. Reentrancy (an actor calling itself through the runtime) is configurable separately.

This single-threaded guarantee is the primary reason to use actors when managing large numbers of isolated stateful objects.

## Communication

Actor method calls are routed via the sidecar:

1. Client calls the actor API on its local sidecar
2. Sidecar checks its cached placement table to find which node hosts the target actor ID
3. Call is forwarded to the target sidecar, which invokes the actor method on the application

The **placement service** maintains the cluster-wide actor-to-node mapping and distributes actors across healthy nodes. On node failure, actors auto-migrate.

Unlike [[wiki/tech/dapr-workflows]] (which use a gRPC pull model requiring no inbound ports), actors require `--app-port` so the sidecar can invoke actor methods on the application.

## State

Actor state is stored in a transactional state store. The component must have `actorStateStore: true` in its metadata. Only one state store component can be used for all actors in an app.

Supported stores: PostgreSQL, MySQL, SQL Server, SQLite, Oracle Database, CockroachDB, MongoDB, Redis.

> [!warning] Redis in production
> Redis does not support transaction rollbacks. Avoid using Redis as the actor state store in production. Use PostgreSQL or another ACID-compliant store (as of 2026-05-03). See also the same constraint in [[wiki/tech/dapr-workflows]].

## Timers vs. Reminders

Actors can schedule periodic work on themselves:

| | Timers | Reminders |
|---|---|---|
| Persisted | No — lost on deactivation | Yes — stored in state store |
| Survives restart | No | Yes |
| Resource cost | Lightweight | Higher (state I/O) |

Use timers for ephemeral, best-effort scheduling. Use reminders for durable, must-fire callbacks (e.g., clearing an alarm 15 seconds after it triggered, even if the host restarts).

## Namespacing

Actor types can be deployed into different namespaces. Actors in one namespace can only call actors in the same namespace.

## When to use actors vs. workflows

| Use actors when… | Use workflows when… |
|---|---|
| Thousands+ of independent stateful objects | Multi-step orchestration across services |
| Single-threaded object semantics needed | Sequence of tasks with retries and compensation |
| No significant cross-actor querying | Long-running processes with external events or human approval |
| Callers won't block on I/O inside the actor | Fan-out/fan-in, monitor, or compensation/saga patterns |

**Key relationship:** [[wiki/tech/dapr-workflows]] is built on top of Dapr Actors. Workflows internally register two actor types per app (`dapr.internal.{ns}.{appID}.workflow` and `dapr.internal.{ns}.{appID}.activity`) and use actor reminders for durable timers. Workflows provide a higher-level abstraction that manages actor activation and lifecycle automatically.

## See also

- [[wiki/tech/dapr-workflows]] — orchestration layer built on top of actors
- [[wiki/tech/dapr-workflow-patterns]] — task chaining, fan-out/fan-in, monitor, compensation/saga
- [[wiki/tech/dapr-go-sdk]] — Go SDK includes actor client support
- [[wiki/tech/dapr-pubsub]] — another Dapr building block for event-driven messaging
**Raw sources:**
- [[raw/ingested/articles/Actors overview]]
- [[raw/ingested/articles/Quickstart Actors]]
