# Index

Content catalog for this wiki. Every page in `wiki/` is listed here with a one-line summary. **Read this file first** when answering queries, then drill into relevant pages.

Updated by the LLM on every ingest, filed analysis, and lint pass.

---

## Models

*(none yet)*

## Concepts

- [[wiki/concepts/llm-wiki-pattern]] — the LLM-maintained wiki pattern: ingest/query/lint operations, three-layer architecture, compounding knowledge base
- [[wiki/concepts/persistent-knowledge-base]] — knowledge base that compiles synthesis once and accumulates it, contrasted with stateless RAG
- [[wiki/concepts/memex]] — Bush's 1945 vision of a private, associative-trail personal knowledge device; spiritual ancestor of this wiki
- [[wiki/concepts/wiki-maintenance]] — why wikis fail at maintenance and how LLMs change the economics

## Trends

*(none yet)*

## Tech

- [[wiki/tech/dapr-pubsub]] — Dapr's broker-agnostic pub/sub API: three subscription types (declarative/programmatic/streaming), at-least-once delivery, CloudEvents, competing consumers, outbox pattern
- [[wiki/tech/dapr-go-sdk]] — Dapr Go SDK: Client (app→Dapr) covers all building blocks; Service/Callback (Dapr→app) via HTTP or gRPC
- [[wiki/tech/dapr-workflows]] — Dapr workflow building block: SDK pattern, determinism rules, retry policy, multi-app, CLI management, concurrency/retention config, architecture internals
- [[wiki/tech/dapr-workflow-patterns]] — Five workflow patterns: task chaining, fan-out/fan-in, monitor (continue-as-new), external system interaction, compensation/saga
- [[wiki/tech/dapr-jobs]] — Dapr Jobs API (alpha): cron scheduling with retry policy and TTL via Dapr sidecar
- [[wiki/tech/dapr-actors]] — Dapr virtual actor building block: turn-based concurrency, actor lifetime, placement service, timers vs reminders, actors vs workflows
- [[wiki/tech/awesome-go]] — curated Go ecosystem reference: AI/LLM tooling, distributed systems, messaging, workflow frameworks

---

## Raw Sources

### Pending

*(none)*

### Ingested

- `raw/ingested/articles/llm-wiki-pattern.md` → [[wiki/concepts/llm-wiki-pattern]]
- `raw/ingested/articles/dapr-pubsub-overview.md` → [[wiki/tech/dapr-pubsub]]
- `raw/ingested/articles/dapr-pubsub-subscription-types.md` → [[wiki/tech/dapr-pubsub]]
- `raw/ingested/articles/Dapr Go SDK.md` → [[wiki/tech/dapr-go-sdk]]
- `raw/ingested/articles/dapr-go-client.md` → [[wiki/tech/dapr-go-sdk]], [[wiki/tech/dapr-workflows]], [[wiki/tech/dapr-jobs]]
- `raw/ingested/articles/dapr-go-service.md` → [[wiki/tech/dapr-go-sdk]]
- `raw/ingested/articles/Publish & subscribe messaging.md` → [[wiki/tech/dapr-pubsub]]
- `raw/ingested/articles/avelinoawesome-go A curated list of awesome Go frameworks, libraries and software.md` → [[wiki/tech/awesome-go]]
- `raw/ingested/articles/Quickstart Workflow.md` → [[wiki/tech/dapr-workflows]]
- `raw/ingested/articles/Workflow overview.md` → [[wiki/tech/dapr-workflows]]
- `raw/ingested/articles/Workflow architecture.md` → [[wiki/tech/dapr-workflows]]
- `raw/ingested/articles/Features and concepts.md` → [[wiki/tech/dapr-workflows]]
- `raw/ingested/articles/How to Author a workflow.md` → [[wiki/tech/dapr-workflows]]
- `raw/ingested/articles/How to Manage workflows.md` → [[wiki/tech/dapr-workflows]]
- `raw/ingested/articles/Multi Application Workflows.md` → [[wiki/tech/dapr-workflows]]
- `raw/ingested/articles/Workflow Execution Concurrency.md` → [[wiki/tech/dapr-workflows]]
- `raw/ingested/articles/History Retention Policy.md` → [[wiki/tech/dapr-workflows]]
- `raw/ingested/articles/Workflow patterns.md` → [[wiki/tech/dapr-workflow-patterns]]
- `raw/ingested/articles/Quickstart Publish and Subscribe.md` → [[wiki/tech/dapr-pubsub]]
- `raw/ingested/articles/Actors overview.md` → [[wiki/tech/dapr-actors]]
- `raw/ingested/articles/Quickstart Actors.md` → [[wiki/tech/dapr-actors]]
