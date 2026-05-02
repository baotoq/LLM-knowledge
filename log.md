# Log

Append-only chronological record of all wiki operations.

Format: `## [YYYY-MM-DD] <op> | <title>`

Parse with: `grep "^## \[" log.md`

---

## [2026-05-02] init | LLM Wiki bootstrap

- Created `CLAUDE.md` — operating schema
- Created `README.md` — human-facing overview (rewrote Obsidian default)
- Created `index.md` — content catalog scaffold
- Created `log.md` — this file
- Created `wiki/overview.md` — top-level synthesis placeholder
- Created `raw/articles/`, `raw/books/`, `raw/papers/`, `raw/notes/`, `raw/assets/`
- Created `wiki/sources/`, `wiki/entities/`, `wiki/concepts/`, `wiki/topics/`, `wiki/analyses/`

## [2026-05-02] ingest | LLM Wiki — A Pattern for Building Personal Knowledge Bases Using LLMs

- Created `raw/articles/llm-wiki-pattern.md` — original idea file (first source)
- Created `wiki/sources/llm-wiki-pattern.md` — structured summary with key claims, quotes, connections, open questions
- Created `wiki/concepts/persistent-knowledge-base.md` — core concept: compounding synthesis vs. stateless RAG
- Created `wiki/concepts/memex.md` — Bush's 1945 precursor; the maintenance gap LLMs close
- Created `wiki/entities/vannevar-bush.md` — originator of the Memex concept
- Created `wiki/topics/wiki-maintenance.md` — why wikis fail; how LLMs change the maintenance economics
- Updated `index.md` — added all 5 new wiki pages
- Updated `wiki/overview.md` — seeded with founding source reference

## [2026-05-02] schema-update | Simplify wiki — remove labs, papers, analyses, raw/papers

- Removed `wiki/labs/`, `wiki/papers/`, `wiki/analyses/`, `raw/papers/` — over-granular for current use
- Updated `CLAUDE.md` — pruned wiki categories, raw categories, frontmatter types, ingest steps, index format
- Updated `index.md` — removed Labs, Papers, Analyses sections

## [2026-05-02] schema-update | Add wiki/tech/ category for software engineering content

- Updated `CLAUDE.md` — added `wiki/tech/` category; expanded domain to cover software engineering alongside AI research; added `tech` to frontmatter type options; updated index category order
- Created `wiki/tech/` directory

## [2026-05-02] ingest | Dapr Pub/Sub

- Created `raw/articles/dapr-pubsub-overview.md` — fetched from docs.dapr.io
- Created `raw/articles/dapr-pubsub-subscription-types.md` — fetched from docs.dapr.io
- Created `wiki/tech/dapr-pubsub.md` — broker-agnostic messaging API, three subscription types, delivery semantics, CloudEvents, competing consumers, outbox pattern, gotchas
- Updated `index.md` — added Tech section and dapr-pubsub entry

## [2026-05-02] schema-update | Restructure for AI research second-brain

- Rewrote `CLAUDE.md` — domain narrowed to AI research; new wiki categories: models / labs / papers / concepts / trends / analyses; new templates for paper and model pages; added "date all capability claims" constraint
- Created `wiki/models/`, `wiki/labs/`, `wiki/papers/`, `wiki/trends/` — new AI research category folders
- Moved `wiki/sources/llm-wiki-pattern.md` → `wiki/concepts/llm-wiki-pattern.md` (type: concept)
- Moved `wiki/topics/wiki-maintenance.md` → `wiki/concepts/wiki-maintenance.md` (type: concept)
- Deleted `wiki/entities/vannevar-bush.md` — entity category removed; person referenced inline in concept pages
- Removed folders: `wiki/sources/`, `wiki/entities/`, `wiki/topics/`, `wiki/libs/`, `wiki/practices/`, `raw/docs/`
- Updated all internal wikilinks across concept pages to reflect new paths
- Updated `wiki/overview.md` — reframed for AI research with key driving questions and suggested first sources
- Updated `index.md` — new category order: Models · Labs · Papers · Concepts · Trends · Analyses

## [2026-05-02] schema-update | Raw pending/ingested folder split

- Created `raw/ingested/articles/`, `raw/ingested/books/`, `raw/ingested/notes/` — physical folders for processed sources
- Moved already-ingested files to `raw/ingested/articles/`: `llm-wiki-pattern.md`, `dapr-pubsub-overview.md`, `dapr-pubsub-subscription-types.md`
- Updated `CLAUDE.md` — raw source categories table, ingest steps (added move step), hard constraints, index format (Raw Sources section)
- Updated `index.md` — added Raw Sources section with Pending/Ingested subsections
- Updated `wiki/tech/dapr-pubsub.md` — fixed raw file paths to `raw/ingested/articles/`

## [2026-05-02] ingest | Dapr Go SDK

- Created `wiki/tech/dapr-go-sdk.md` — Client SDK (app→Dapr) and Service/Callback SDK (Dapr→app) overview; Client/Service split and relationship to pub/sub subscription types
- Moved `raw/articles/Dapr Go SDK.md` → `raw/ingested/articles/Dapr Go SDK.md`
- Updated `index.md` — added `dapr-go-sdk` to Tech section; added entry to Raw Sources Ingested

## [2026-05-03] ingest | Dapr Workflow Quickstart

- Updated `wiki/tech/dapr-workflows.md` — major expansion: Go API discrepancy (NewWorker vs NewRegistry pattern), external event handling with WaitForExternalEvent, full workflow management CLI (list/history/raise-event/suspend/resume/purge), Redis production warning, purge-requires-running-client gotcha
- Added [!warning] callout for API pattern discrepancy between earlier and current Go SDK docs
- Moved `raw/articles/Quickstart Workflow.md` → `raw/ingested/articles/`
- Updated `index.md` — added raw file to Ingested

## [2026-05-02] ingest | Awesome Go — curated Go ecosystem reference

- Created `wiki/tech/awesome-go.md` — curated summary of avelino/awesome-go focusing on AI/LLM tooling, distributed systems, messaging, and workflow frameworks
- Key observations: Go AI ecosystem active but 12–18 months behind Python; Temporal is main Dapr Workflows alternative; Watermill is main library-native Dapr pub/sub alternative; NATS has rich Go ecosystem
- Moved `raw/articles/avelinoawesome-go A curated list of awesome Go frameworks, libraries and software.md` → `raw/ingested/articles/`
- Updated `index.md` — added `awesome-go` to Tech section; updated Raw Sources

## [2026-05-02] lint | Audit and fix wiki issues

- Fixed wikilink format inconsistency in `wiki/overview.md` — short-form `[[concepts/...]]` → full-path `[[wiki/concepts/...]]`
- Removed non-standard `raw:` frontmatter field from `wiki/tech/dapr-pubsub.md` and `wiki/concepts/llm-wiki-pattern.md`
- Removed model-page style `**Author/Date/Type:**` header from `wiki/concepts/llm-wiki-pattern.md`
- Updated `wiki/overview.md` — replaced stale "Bootstrap state" with current two-cluster description; removed deleted `wiki/labs/` reference
- Updated `wiki/concepts/persistent-knowledge-base.md` — corrected stale category list from `models/labs/papers/concepts/trends/analyses` to `models/concepts/trends/tech`
- Added cross-references: `wiki/tech/dapr-workflows.md` and `wiki/tech/dapr-jobs.md` now link to `dapr-pubsub`; `wiki/tech/dapr-pubsub.md` now links to `dapr-go-sdk`, `dapr-workflows`, `dapr-jobs`
- Ingested `raw/articles/Publish & subscribe messaging.md` → `wiki/tech/dapr-pubsub` (navigation index page, content already covered); moved to `raw/ingested/articles/`
- Updated `CLAUDE.md` — added "Installed skills and directory conventions" section noting that `second-brain-*` skill directory conventions (`wiki/sources/`, `wiki/entities/`, `wiki/synthesis/`) are intentionally superseded by this schema

## [2026-05-03] schema-update | Raw file references use wikilinks

- Updated `CLAUDE.md` — changed raw file reference convention from relative paths to `[[wikilinks]]` so they are clickable in Obsidian
- Updated all 8 `wiki/tech/` pages to use the new format

## [2026-05-03] ingest | Dapr Workflows — full documentation batch (9 articles)

- Updated `wiki/tech/dapr-workflows.md` — major rewrite: determinism rules (CurrentUTCDateTime, no goroutines, no direct I/O), retry policy (Go example with RetryPolicy struct), multi-app workflows (WithActivityAppID/WithChildWorkflowAppID, v1.16+, WorkflowsRemoteActivityReminder feature gate v1.17), full CLI section (run/rerun/terminate/purge with all flags), scheduler reminder management (dapr scheduler list/get/delete/export/import), concurrency limits config, history retention policy config, architecture internals (gRPC pull model, two actor types, state store key structure, throughput trick with WithStartTime)
- Created `wiki/tech/dapr-workflow-patterns.md` — five workflow patterns with Go examples: task chaining, fan-out/fan-in (workflow.NewTaskSlice), monitor (ContinueAsNew — never use for{}), external system interaction (WaitForExternalEvent with timeout), compensation/saga (reverse-order activity rollback); async HTTP API pattern
- Updated `wiki/tech/dapr-pubsub.md` — added component YAML template, Go subscriber/publisher function signatures (eventHandler, PublishEvent), updated raw sources list
- Moved 10 raw articles to `raw/ingested/articles/`: Workflow overview, Workflow architecture, Features and concepts, How to Author a workflow, How to Manage workflows, Multi Application Workflows, Workflow Execution Concurrency, History Retention Policy, Workflow patterns, Quickstart Publish and Subscribe
- Updated `index.md` — added dapr-workflow-patterns to Tech section; updated dapr-workflows description; added all 10 raw files to Raw Sources Ingested

## [2026-05-03] ingest | Dapr Actors — overview and quickstart

- Created `wiki/tech/dapr-actors.md` — virtual actor pattern, turn-based concurrency, placement service, timers vs reminders, actors vs workflows decision guide, actors-as-workflow-foundation relationship
- Updated `wiki/tech/dapr-workflows.md` — added cross-reference to dapr-actors in architecture internals (actor types section) and See also
- Moved `raw/articles/Actors overview.md` → `raw/ingested/articles/`
- Moved `raw/articles/Quickstart Actors.md` → `raw/ingested/articles/`
- Updated `index.md` — added dapr-actors to Tech section; added both raw files to Raw Sources Ingested

## [2026-05-02] ingest | Dapr Go Client SDK + Go Service SDK

- Created `raw/ingested/articles/dapr-go-client.md` — fetched from docs.dapr.io
- Created `raw/ingested/articles/dapr-go-service.md` — fetched from docs.dapr.io
- Updated `wiki/tech/dapr-go-sdk.md` — expanded Client SDK with full building blocks table (workflows, jobs, actors, secrets, lock, config, crypto, bindings); added Service SDK HTTP vs gRPC detail; added auth and error handling sections
- Created `wiki/tech/dapr-workflows.md` — worker pattern: register workflow/activity functions, start worker, schedule/wait via client
- Created `wiki/tech/dapr-jobs.md` — alpha Jobs API: cron scheduling, retry policy, TTL via `ScheduleJobAlpha1`
- Updated `index.md` — added dapr-workflows, dapr-jobs to Tech section; added both raw files to Raw Sources Ingested
