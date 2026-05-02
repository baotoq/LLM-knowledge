---
type: tech
tags: [dapr, jobs, scheduling, microservices, distributed-systems]
created: 2026-05-02
updated: 2026-05-02
sources: []
---

# Dapr Jobs API

Dapr's Jobs building block (alpha as of 2026-05-02) enables scheduling work via the Dapr sidecar. Supports cron expressions, retry policies, and TTL configurations.

## Go SDK

```go
resp, err := client.ScheduleJobAlpha1(ctx, &dapr.Job{
    Name:     "my-job",
    Schedule: "@every 10s", // cron or interval expression
    // retry policy, TTL...
})
```

## Features

- Cron expressions and interval syntax (e.g. `@every 10s`, `0 * * * *`)
- Retry policy on job failure
- TTL — job expires and stops firing after a duration

> [!note] Alpha status (2026-05-02)
> The `Alpha1` suffix in `ScheduleJobAlpha1` signals this API is not yet stable and may change.

## See also

- [[wiki/tech/dapr-go-sdk]] — Jobs is one of the Client SDK building blocks
- [[wiki/tech/dapr-pubsub]] — another Dapr building block; event-driven messaging
- [[wiki/tech/dapr-workflows]] — long-running orchestration, also via the Client SDK
**Raw sources:**
- [[raw/ingested/articles/dapr-go-client]]
