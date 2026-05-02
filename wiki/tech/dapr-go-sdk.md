---
type: tech
tags: [dapr, go, sdk, microservices, distributed-systems]
created: 2026-05-02
updated: 2026-05-02
sources: []
---

# Dapr Go SDK

The official Go SDK for building Dapr applications. Wraps every Dapr building block in idiomatic Go across two packages: **Client** (app→Dapr, outbound calls) and **Service/Callback** (Dapr→app, inbound handlers).

## Import

```go
import "github.com/dapr/go-sdk/client"
```

## Client SDK — building blocks

| Building block | Key methods |
|----------------|-------------|
| Service invocation | `InvokeMethod`, `InvokeMethodWithContent` |
| State management | `SaveState`, `GetState`, `DeleteState`, `SaveBulkState`, `QueryState` |
| Pub/Sub | `PublishEvent` (single), `PublishEvents` (batch) |
| Workflows | `ScheduleNewWorkflow`, `WaitForWorkflowCompletion` |
| Jobs | `ScheduleJobAlpha1` — cron expressions, retry policy, TTL |
| Output bindings | `InvokeOutputBinding`, `InvokeBinding` (with response) |
| Actors | Register actor types, invoke methods via actor client |
| Secrets | `GetSecret` |
| Distributed lock | `TryLockAlpha1` |
| Configuration | `GetConfigurationItem`, `SubscribeConfigurationItems` |
| Cryptography | `Encrypt`, `Decrypt` — streaming, requires component + key name |

## Service SDK — two implementations

| Type | Protocol | Use when |
|------|----------|----------|
| HTTP service | HTTP | Simple callback handlers, existing HTTP apps |
| gRPC service | gRPC | High-performance, strongly-typed communication |

Both register handlers that Dapr calls: subscription endpoints, binding triggers, service invocation handlers.

## Relationship to pub/sub subscription types

- **Streaming subscriptions** — Client SDK (app pulls; no HTTP endpoint needed)
- **Declarative / programmatic subscriptions** — Service SDK (Dapr pushes to registered HTTP/gRPC endpoint)

See [[wiki/tech/dapr-pubsub]] for subscription type details.

## Authentication

```go
// Via env var
os.Setenv("DAPR_API_TOKEN", "token")

// Explicit
client.WithAuthToken("token")
```

## Error handling

Follows gRPC's richer error model — parse status codes, messages, and detail objects: `ErrorInfo`, `BadRequest`, `ResourceInfo`, `Help`.

## See also

- [[wiki/tech/dapr-pubsub]] — pub/sub building block detail
- [[wiki/tech/dapr-workflows]] — workflow worker pattern
- [[wiki/tech/dapr-jobs]] — Jobs API scheduling
- [Go Client SDK docs](https://docs.dapr.io/developing-applications/sdks/go/go-client/)
- [Go Service SDK docs](https://docs.dapr.io/developing-applications/sdks/go/go-service/)
- Raw: `raw/ingested/articles/Dapr Go SDK.md`, `raw/ingested/articles/dapr-go-client.md`, `raw/ingested/articles/dapr-go-service.md`
