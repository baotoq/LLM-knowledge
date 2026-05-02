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
