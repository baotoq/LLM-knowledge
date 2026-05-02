---
title: "Dapr Go Client SDK"
source: "https://docs.dapr.io/developing-applications/sdks/go/go-client/"
author:
published:
created: 2026-05-02
description: "Go Client SDK for interacting with all Dapr building blocks"
tags:
  - "raw"
---

# Getting Started with the Dapr Client Go SDK

## Overview

The Dapr Go client package enables interaction with other Dapr applications from Go code. Provides easy-to-use methods for building distributed applications.

## Prerequisites

- Dapr CLI installed
- Initialized Dapr environment
- Go installed

## Core Import

```go
import "github.com/dapr/go-sdk/client"
```

## Error Handling

Dapr errors follow gRPC's richer error model, allowing developers to parse status codes, messages, and detailed error information including ErrorInfo, BadRequest, ResourceInfo, and Help objects.

## Key Building Blocks

### Service Invocation

Two primary patterns:
- Without data: `client.InvokeMethod(ctx, "app-id", "method-name", "post")`
- With data: `client.InvokeMethodWithContent(ctx, ...)` accepting DataContent objects

### State Management

- `SaveState()` - persist data with key
- `GetState()` - retrieve stored values
- `DeleteState()` - remove entries
- `SaveBulkState()` - multiple items simultaneously
- `QueryState()` - filter and sort stored data

### Publish/Subscribe

Two publication methods:
- Single: `client.PublishEvent(ctx, "component-name", "topic-name", data)`
- Batch: `client.PublishEvents()` for multiple messages

### Workflows

Worker pattern: register workflow and activity functions, start the worker, then manage execution through a workflow client using `ScheduleNewWorkflow()` and `WaitForWorkflowCompletion()`.

### Jobs

The Jobs API enables scheduling with `ScheduleJobAlpha1()`, supporting cron expressions, retry policies, and TTL configurations.

### Output Bindings

- Output-only: `client.InvokeOutputBinding()`
- With response: `client.InvokeBinding()`

### Actors

Create actor types, register them, and invoke methods through an actor client instance.

### Secrets Management

```go
secret, err := client.GetSecret(ctx, "store-name", "secret-name", options)
```

### Distributed Lock

```go
resp, err := client.TryLockAlpha1(ctx, "lockstore", &dapr.LockRequest{...})
```

### Configuration

- Get items: `client.GetConfigurationItem()`
- Subscribe to changes: `client.SubscribeConfigurationItems()`

### Cryptography

High-level streaming APIs:
- `client.Encrypt()` - requires ComponentName, KeyName, Algorithm
- `client.Decrypt()` - requires only ComponentName

## Authentication

Two configuration approaches:
1. Environment variable: `DAPR_API_TOKEN`
2. Explicit method: `client.WithAuthToken("token")`

## Resources

Complete examples available in the [Go SDK Examples repository](https://github.com/dapr/go-sdk/tree/main/examples).
