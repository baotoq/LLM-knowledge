---
type: concept
tags: [llm, knowledge-management, rag, wiki, pattern]
created: 2026-05-02
updated: 2026-05-02
sources: []
---

# LLM Wiki — A Pattern for Building Personal Knowledge Bases Using LLMs

> An LLM incrementally builds and maintains a persistent, interlinked wiki from raw sources — replacing RAG's stateless per-query retrieval with a compounding, pre-synthesized knowledge base that gets richer with every source added.

## Key claims

- RAG re-derives answers from scratch on every query; the LLM Wiki compiles knowledge once and keeps it current — the wiki is a **persistent, compounding artifact**
- Three layers: immutable raw sources / LLM-owned wiki / schema document (CLAUDE.md) that defines conventions and workflows
- Three core operations: **Ingest** (read → discuss → write summary → update linked pages → update index + log), **Query** (index → relevant pages → synthesize with citations → optionally file the answer), **Lint** (find contradictions, orphans, gaps, stale claims)
- `index.md` (content catalog) + `log.md` (append-only event log) are the two navigation primitives; the index avoids the need for embedding-based RAG even at ~100 sources / hundreds of pages
- Good query answers should be filed back into the wiki as `wiki/analyses/` pages — explorations compound the knowledge base just like ingested sources do
- The human's role: curate sources, direct analysis, ask good questions. The LLM's role: everything else (summarizing, cross-referencing, bookkeeping)

## Quotes

> "The wiki keeps getting richer with every source you add and every question you ask."

> "Humans abandon wikis because the maintenance burden grows faster than the value. LLMs don't get bored, don't forget to update a cross-reference, and can touch 15 files in one pass."

> "Obsidian is the IDE; the LLM is the programmer; the wiki is the codebase."

> "The part he couldn't solve was who does the maintenance. The LLM handles that."

## Connections

- [[wiki/concepts/persistent-knowledge-base]] — the central concept this pattern instantiates
- [[wiki/concepts/memex]] — Bush's 1945 precursor; the wiki pattern is its practical realization
- [[wiki/concepts/wiki-maintenance]] — the maintenance problem that kills community wikis; LLMs solve it

## Open questions

- At what scale does `index.md`-based navigation break down, and when should a search tool be added?
- How do you handle image-heavy sources where the LLM can't read the full document in one pass?
- What does the schema look like for team/multi-author wikis with human review loops?
- How should the wiki handle time-sensitive sources (news, benchmarks) that become stale quickly?
