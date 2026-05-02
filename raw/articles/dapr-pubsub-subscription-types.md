---
title: Dapr Pub/Sub Subscription Types
source: https://docs.dapr.io/developing-applications/building-blocks/pubsub/subscription-methods/
created: 2026-05-02
tags: [dapr, pubsub, subscriptions, microservices]
---

# Dapr Pub/Sub Subscription Types

## Overview

Dapr supports three subscription methods for message topics, each with distinct characteristics:

| Type | Definition Location | Key Feature |
|------|-------------------|-------------|
| **Declarative** | External YAML file | Removes the Dapr dependency from your code |
| **Streaming** | Application code | Dynamic runtime management without requiring subscription endpoints |
| **Programmatic** | Application code | Static subscriptions read at startup requiring application endpoints |

## Declarative Subscriptions

Defined in external YAML component files:

```yaml
apiVersion: dapr.io/v2alpha1
kind: Subscription
metadata:
  name: order
spec:
  topic: orders
  routes:
    default: /orders
  pubsubname: pubsub
scopes:
- orderprocessing
```

- Decouples subscription logic from application code
- Supports hot-reload (preview feature gate)
- Requires HTTP endpoint in application matching the defined route
- Ideal for integrating existing applications without code modifications

## Streaming Subscriptions

Streaming subscriptions are fundamentally different — messages are **pulled** by the application from Dapr, not pushed to an endpoint.

- No endpoint required in application
- Dynamic addition/removal of subscriptions at runtime
- Can subscribe without app sidecar configuration

**Pull-based (blocking):** Applications call methods like `next_message()` to retrieve messages sequentially.

**Handler-based (non-blocking):** Callback functions execute in separate threads for each received message.

Limitations: No route or bulk subscription concepts.

## Programmatic Subscriptions

Subscriptions defined in application startup code:

```python
@app.route('/dapr/subscribe', methods=['GET'])
def subscribe():
    subscriptions = [{
      'pubsubname': 'pubsub',
      'topic': 'orders',
      'routes': {...}
    }]
    return jsonify(subscriptions)
```

- Read once during application startup only
- Cannot dynamically add subscriptions at runtime
- Requires `/dapr/subscribe` endpoint configuration

## Comparative Summary

**Declarative** suits applications requiring zero code changes and infrastructure-as-code approaches. **Streaming** enables runtime flexibility without endpoint overhead. **Programmatic** integrates subscriptions directly into application logic for tightly coupled scenarios.
