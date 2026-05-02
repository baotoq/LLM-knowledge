---
type: tech
tags: [dapr, pubsub, microservices, distributed-systems, messaging, event-driven]
created: 2026-05-02
updated: 2026-05-02
sources: []
raw: raw/articles/dapr-pubsub-overview.md
---

# Dapr Pub/Sub

Dapr's publish-subscribe building block provides a **broker-agnostic messaging API** for event-driven microservices — swap the underlying broker (Redis, Kafka, Azure Service Bus, NATS, GCP Pub/Sub, RabbitMQ) by changing a component config file, not application code.

## Three-layer architecture

```
Your service  →  Dapr pub/sub API  →  pluggable component  →  broker
```

The abstraction is genuine: the same publish/subscribe calls work across all supported brokers.

## Three subscription types

| Type | Where defined | Endpoint needed? | Dynamic? |
|------|--------------|-----------------|---------|
| Declarative | External YAML | Yes | Hot-reload (preview) |
| Programmatic | Code at startup | Yes | No — static only |
| Streaming | Code at runtime | **No** | Yes — add/remove anytime |

**Key insight:** Streaming subscriptions are fundamentally different — the app *pulls* messages from Dapr rather than having them pushed to an HTTP endpoint. No route configuration needed; subscriptions can be added or removed at runtime.

## Delivery semantics

- **At-least-once** guaranteed across all components — automatic redelivery on failure
- Subscribers control outcome explicitly: return success (delivered), `RETRY`, or `DROP`
- **Dead letter topics** catch messages that keep failing, preventing infinite retry loops

## CloudEvents

Messages are automatically wrapped in [CloudEvents 1.0](https://cloudevents.io/) envelopes. This enables:
- Content-based routing — route to different handlers based on CloudEvent fields
- Interoperability with other CloudEvents-compatible systems

Disable per-message with a header flag for non-Dapr consumers.

## Competing consumers

Multiple app instances with the same `app-id` each receive a message only once (load-balanced, not broadcast). **Broker-dependent** — only works with: Kafka, Azure Service Bus Queues, RabbitMQ, Redis Streams.

If your broker isn't on that list, all instances receive every message.

## Outbox pattern

Dapr supports atomic transactions spanning both a state store write and a message publish — no two-phase commit needed. Useful for ensuring a database write and an event are never split.

## Topic scoping

Restrict which app IDs can publish or subscribe to specific topics via component metadata. Useful for multi-tenant or security-sensitive deployments.

## Namespace consumer groups

Pass a `namespace` value in component metadata to isolate same-app-id instances across tenants. Instances in different namespaces consume independently from the same topic.

## Other features

- **Message TTL** — discard unread messages after a timeout; prevents queue buildup
- **Bulk publish/subscribe** — batch multiple messages in one API call for throughput
- **StatefulSet scaling** — use `{podName}` as `consumerID` in Kubernetes for sticky partition assignment

## Gotchas

- Competing consumers is **not universal** — check your broker supports it before relying on it
- Programmatic subscriptions are **read once at startup** — cannot add topics dynamically; use streaming for that
- Streaming subscriptions have **no bulk or route support** — simpler API, fewer features

## See also

- [Dapr pub/sub overview](https://docs.dapr.io/developing-applications/building-blocks/pubsub/pubsub-overview/)
- [Subscription methods](https://docs.dapr.io/developing-applications/building-blocks/pubsub/subscription-methods/)
- Raw: `raw/ingested/articles/dapr-pubsub-overview.md`, `raw/ingested/articles/dapr-pubsub-subscription-types.md`
