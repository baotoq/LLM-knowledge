---
title: Dapr Publish & Subscribe Overview
source: https://docs.dapr.io/developing-applications/building-blocks/pubsub/pubsub-overview/
created: 2026-05-02
tags: [dapr, pubsub, microservices, distributed-systems]
---

# Dapr Publish and Subscribe Overview

## Core Concept

Publish and subscribe enables microservices communication through event-driven architectures. Publishers send messages to topics without knowing which applications will receive them, while subscribers receive messages from topics without knowing the producer. An intermediary message broker distributes each message from publishers to all interested subscribers.

## Pub/Sub API Architecture

The Dapr pub/sub API provides:
- Platform-agnostic message sending and receiving
- At-least-once message delivery guarantee
- Integration with various message brokers and queuing systems

The system operates in three layers:
1. Your service calls the Dapr pub/sub building block API
2. The building block invokes a pluggable Dapr pub/sub component
3. The component encapsulates a specific message broker (Redis Streams, NATS, Azure Service Bus, GCP pub/sub, etc.)

## Key Features

**CloudEvents Format**
Messages automatically wrap in CloudEvents 1.0 specification envelopes, using the `Content-Type` header for the `datacontenttype` attribute. Applications can disable CloudEvent wrapping for interoperability with non-Dapr systems.

**Subscription Types**
Three subscription approaches exist:
- **Declarative**: External file configuration, removes code dependency
- **Streaming**: Runtime-dynamic subscriptions defined in code, requires no endpoint
- **Programmatic**: Static code-based subscriptions requiring application endpoints

**Message Delivery & Routing**
Dapr considers messages delivered once subscribers respond without errors. Subscribers can use explicit statuses (RETRY, DROP) for granular control. Content-based routing patterns allow routing CloudEvents to different handlers based on message contents.

**Dead Letter Topics**
Prevents infinite retry loops for unprocessable messages caused by application errors or state issues.

**Outbox Pattern**
Enables single transactions across transactional state stores and message brokers.

**Namespace Consumer Groups**
Multi-tenancy support through namespace values in component metadata, allowing same-app-id instances across different namespaces to coexist.

**At-Least-Once Guarantee**
All Dapr pub/sub components ensure messages are delivered at least once to every subscriber, with automatic redelivery on failures.

**Competing Consumers Pattern**
Multiple application instances sharing an app-id receive each message only once. Supported by Kafka, Azure Service Bus Queues, RabbitMQ, and Redis Streams.

**Topic Scoping**
Restricts which applications can publish or subscribe to specific topics for security.

**Message TTL**
Per-message timeouts discard unread messages, preventing queue buildup.

**Bulk Operations**
Send and receive multiple messages in single requests for higher throughput.

**StatefulSet Scaling**
Kubernetes deployments can use sticky `consumerID` per instance with StatefulSets using `{podName}` markers.
