---
title: Publish & subscribe messaging
source: https://docs.dapr.io/developing-applications/building-blocks/pubsub/
author:
  - "[[Add Reo.dev and cookie banner (#5147) (568d9aa)]]"
published: 2026-04-24
created: 2026-05-02
description: Secure, scalable messaging between services
tags:
  - tech
---
## Publish & subscribe

> [!primary] More about Dapr Pub/sub
> Learn more about how to use Dapr Pub/sub:
> 
> - Try the [Pub/sub quickstart](https://docs.dapr.io/getting-started/quickstarts/pubsub-quickstart/).
> - Explore pub/sub via any of the supporting [Dapr SDKs](https://docs.dapr.io/developing-applications/sdks/).
> - Review the [Pub/sub API reference documentation](https://docs.dapr.io/reference/api/pubsub_api/).
> - Browse the supported [pub/sub component specs](https://docs.dapr.io/reference/components-reference/supported-pubsub/).

---

##### Publish and subscribe overview

Overview of the pub/sub API building block

##### How to: Publish a message and subscribe to a topic

Learn how to send messages to a topic with one service and subscribe to that topic in another service

##### Publishing & subscribing messages with Cloudevents

Learn why Dapr uses CloudEvents, how they work in Dapr pub/sub, and how to create CloudEvents.

##### Publishing & subscribing messages without CloudEvents

Learn when you might not use CloudEvents and how to disable them.

##### How-To: Route messages to different event handlers

Learn how to route messages from a topic to different event handlers based on CloudEvent fields

##### Declarative, streaming, and programmatic subscription types

Learn more about the subscription types that allow you to subscribe to message topics.

##### Dead Letter Topics

Use subscription dead letter topics to forward undeliverable messages

##### How to: Set up pub/sub namespace consumer groups

Learn how to use the metadata-based namespace consumer group in your component

##### How to: Horizontally scale subscribers with StatefulSets

Learn how to subscribe with StatefulSet and scale horizontally with consistent consumer IDs

##### Scope Pub/sub topic access

Use scopes to limit pub/sub topics to specific applications

##### Message Time-to-Live (TTL)

Use time-to-live in pub/sub messages.

##### Publish and subscribe to bulk messages

Learn how to use the bulk publish and subscribe APIs in Dapr.