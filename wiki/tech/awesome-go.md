---
type: tech
tags: [go, golang, libraries, reference, distributed-systems, ai, messaging, workflows]
created: 2026-05-02
updated: 2026-05-02
sources: []
---

# Awesome Go — Curated Reference

A curated catalog of Go frameworks, libraries, and software maintained at [avelino/awesome-go](https://github.com/avelino/awesome-go). 3700+ entries across 60+ categories. This page highlights the sections most relevant to this wiki's focus: AI tooling, distributed systems, messaging, and workflow orchestration.

## AI and Machine Learning in Go

The Go AI ecosystem is nascent compared to Python but actively growing (as of 2026-05-02).

### LLM integration

| Library | What it does |
|---------|-------------|
| `langchaingo` | LangChain port for Go — LLM chains, agents, tools |
| `goai` | Unified SDK across 20+ providers, inspired by Vercel AI SDK |
| `LocalAI` | Self-hosted OpenAI-compatible API server; run local models |
| `Ollama` | Run large language models locally |
| `chromem-go` | Embeddable vector DB (Chroma-like interface), zero third-party deps, in-memory with optional persistence |
| `fun` | Minimal LLM interface for Go |

### Agent frameworks

| Library | What it does |
|---------|-------------|
| `langgraphgo` | LangGraph port — stateful multi-actor LLM apps |
| `agent-sdk-go` | Durable AI agents on Temporal with tools, MCP, human approvals |
| `AegisFlow` | AI gateway for routing/securing LLM traffic across 10+ providers |
| `routex` | YAML-driven multi-agent runtime with MCP and Erlang-style supervision |

### ML inference

| Library | What it does |
|---------|-------------|
| `hugot` | HuggingFace transformer pipelines via ONNX runtime |
| `onnx-go` | Go interface to ONNX models |
| `GoMLX` | Accelerated ML framework for Go (XLA-backed) |

**Key observation (2026-05-02):** Go AI tooling is following Python's LangChain/agent pattern but lags 12–18 months behind. The strongest niche is inference/serving (Ollama, LocalAI) and vector storage (chromem-go), not training.

## Distributed Systems

| Library | What it does |
|---------|-------------|
| `grpc-go` | Standard gRPC for Go |
| `go-kit` | Microservice toolkit — service discovery, load balancing, tracing |
| `NATS` | Simple, secure, high-performance messaging system |
| `Temporal` | Durable execution — fault-tolerant, stateful long-running processes |
| `Kratos` | Modular microservices framework |
| `go-micro` | Distributed systems framework |
| `raft` (HashiCorp / CoreOS) | Raft consensus protocol implementations |
| `outbox` / `outboxer` | Transactional outbox pattern implementations |
| `dragonboat` | High-performance multi-group Raft library |

### Temporal vs Dapr Workflows

Both `Temporal` and [[wiki/tech/dapr-workflows]] solve fault-tolerant long-running process orchestration. Key differences:

| | Temporal | Dapr Workflows |
|-|----------|---------------|
| Model | Durable execution (code-native) | Dapr sidecar building block |
| State | Managed by Temporal server | Managed by Dapr runtime |
| Language | SDK per language | SDK per language |
| Infrastructure | Separate Temporal cluster | Dapr sidecar |
| Best for | Workflow-first systems | Already using Dapr |

## Messaging

| Library          | What it does                                                                                    |
| ---------------- | ----------------------------------------------------------------------------------------------- |
| `Watermill`      | Event-driven apps, event sourcing, CQRS, sagas — works with Kafka, RabbitMQ, HTTP, MySQL binlog |
| `NATS Go Client` | Official Go client for NATS (also a [[wiki/tech/dapr-pubsub]] broker)                           |
| `sarama`         | Shopify's Apache Kafka client                                                                   |
| `Asynq`          | Distributed task queue on Redis                                                                 |
| `machinery`      | Async task queue via distributed message passing                                                |
| `Watermill`      | Strong alternative to Dapr pub/sub for event-driven Go apps without a sidecar                   |
| `Go-MediatR`     | Mediator + CQRS patterns for event-driven architecture                                          |

### Watermill vs Dapr Pub/Sub

`Watermill` is the closest library-native alternative to [[wiki/tech/dapr-pubsub]]:

| | Watermill | Dapr Pub/Sub |
|-|-----------|-------------|
| Model | Library (in-process) | Sidecar (out-of-process) |
| Broker abstraction | Yes (Kafka, RabbitMQ, etc.) | Yes (same brokers + more) |
| Event sourcing / CQRS | First-class | Not built-in |
| Infrastructure | Just the library | Dapr sidecar required |
| Best for | Go-native event-driven without Dapr | Already using Dapr |

## Workflow Frameworks

| Library | What it does |
|---------|-------------|
| `Temporal` (sdk-go) | Durable execution system — see Distributed Systems above |
| `Cadence-client` | Uber's predecessor to Temporal |
| `Dagu` | No-code DAG workflow executor via YAML |
| `workflow` (luno) | Tech-stack agnostic event-driven workflow |

## See also

- [[wiki/tech/dapr-pubsub]] — Dapr's pub/sub building block; Watermill and NATS are alternatives/complements
- [[wiki/tech/dapr-workflows]] — Dapr's workflow building block; Temporal is the main alternative
- [[wiki/tech/dapr-go-sdk]] — official Go SDK for Dapr
- [avelino/awesome-go](https://github.com/avelino/awesome-go) — full canonical list
**Raw sources:**
- [[raw/ingested/articles/avelinoawesome-go A curated list of awesome Go frameworks, libraries and software]]
