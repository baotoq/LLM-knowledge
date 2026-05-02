# LLM Knowledge Wiki

A personal second-brain wiki maintained by Claude Code. Raw sources live in `raw/`; the synthesized wiki lives in `wiki/`. The operating schema is in `CLAUDE.md`.

## How to use

1. Drop a source into the appropriate `raw/` subfolder — or paste content and ask Claude to save and ingest it.
2. Tell Claude to **ingest** it. It will discuss key takeaways, create a summary page in `wiki/sources/`, and update all relevant entity, concept, and topic pages.
3. **Ask questions.** Claude reads `index.md`, drills into relevant pages, and answers with citations.
4. Periodically ask Claude to **lint the wiki** — it will surface contradictions, orphan pages, and gaps.

## Structure

```
raw/          immutable source documents (articles, books, papers, notes)
wiki/         LLM-maintained synthesized knowledge
index.md      catalog of every wiki page (LLM reads this first on every query)
log.md        append-only event history
CLAUDE.md     operating schema — rules the LLM follows
```

See `CLAUDE.md` for the full schema and operation details.
