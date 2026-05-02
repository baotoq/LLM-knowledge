---
type: concept
tags: [knowledge-management, llm, rag, architecture]
created: 2026-05-02
updated: 2026-05-02
sources: [wiki/concepts/llm-wiki-pattern]
---

# Persistent Knowledge Base

A knowledge base that **accumulates synthesized structure over time** rather than re-deriving answers from raw documents at every query.

The key distinction from standard retrieval-augmented generation (RAG): in RAG, the LLM is a stateless processor — it reads raw chunks and generates an answer at query time, then discards everything. A persistent knowledge base compiles the knowledge *once*, integrating new sources into an evolving wiki of summaries, concept pages, and cross-references. Subsequent queries read the pre-synthesized wiki rather than raw documents.

## Properties

- **Compounding** — each new source enriches pages that already exist; the value of the whole grows faster than the sum of parts
- **Traceable** — every synthesized claim links back to its source page
- **Contradiction-aware** — conflicts between sources are flagged explicitly rather than silently resolved or missed
- **Human-readable** — the knowledge lives in plain markdown, browsable in any editor (Obsidian, VS Code, etc.)
- **Maintainable at near-zero cost** — an LLM can touch 10–15 pages per ingest without fatigue

## Contrast with RAG

| | RAG | Persistent KB |
|-|-----|--------------|
| Query-time work | Re-read raw docs, re-synthesize | Read pre-built wiki pages |
| Accumulation | None — each query starts fresh | Yes — wiki grows richer over time |
| Cross-references | Discovered on demand | Pre-built during ingest |
| Contradiction handling | Implicit / missed | Explicit `[!warning]` callouts |
| Infrastructure | Embedding store, vector DB | Plain markdown files |

## Realization in this vault

The [[wiki/concepts/llm-wiki-pattern]] document describes the specific pattern instantiated here: raw sources → LLM ingest → structured wiki (models / labs / papers / concepts / trends / analyses) → index-based query.

## See also

- [[wiki/concepts/memex]] — 1945 precursor concept by Vannevar Bush
- [[wiki/concepts/wiki-maintenance]] — why human-maintained wikis fail; how LLMs change the economics
