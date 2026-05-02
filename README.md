# LLM Knowledge Wiki

A personal AI research second-brain maintained by Claude Code. Raw sources live in `raw/`; the synthesized wiki lives in `wiki/`. The operating schema is in `CLAUDE.md`.

## How to use

1. Drop a source into `raw/articles/` — or paste content and ask Claude to save and ingest it.
2. Tell Claude to **ingest** it. It will discuss key takeaways with you, then create/update pages across `wiki/models/`, `wiki/concepts/`, `wiki/trends/`, and `wiki/tech/`.
3. **Ask questions.** Claude reads `index.md`, drills into relevant pages, and answers with citations.
4. Periodically ask Claude to **lint the wiki** — it will surface contradictions, stale claims, orphan pages, and gaps.

## Structure

```
raw/
  articles/     blog posts, news, interviews, papers, docs (Obsidian Web Clipper or paste)
  books/        book chapters, highlights
  notes/        personal observations, brainstorms
  assets/       images and diagrams

wiki/
  overview.md   top-level synthesis and research thesis
  models/       one page per AI model (GPT-4o, Claude 3.5, Llama 3, ...)
  concepts/     technical ideas (transformers, RLHF, scaling laws, ...)
  trends/       macro themes (AGI timelines, AI safety, agentic AI, ...)
  tech/         software engineering docs (libraries, frameworks, tools)

index.md        catalog of every wiki page (Claude reads this first on every query)
log.md          append-only event history
CLAUDE.md       operating schema — rules Claude follows
```

---

## Ingesting a source

### Option A — File already in `raw/`

Save the file to `raw/articles/` first (e.g. via Obsidian Web Clipper), then:

```
Ingest raw/articles/openai-o3-announcement.md
```

```
Ingest raw/articles/attention-is-all-you-need.md
```

```
Ingest raw/articles/dapr-pubsub-overview.md
```

### Option B — Paste content directly

```
Save this to raw/articles/anthropic-interpretability-2025.md and ingest it:

<paste content here>
```

### Option C — Ask Claude to fetch it

```
Fetch https://... and ingest it as raw/articles/slug.md
```

### What happens during ingest

Claude will:
1. Read the source fully
2. **Discuss** key takeaways, surprising claims, and open questions with you before writing anything
3. Create the primary wiki page (model, concept, trend, or tech page)
4. Update or create all linked pages (models, concepts, trends mentioned)
5. Update `index.md` and append to `log.md`

---

## Querying the wiki

```
What do I know about scaling laws?
```

```
Compare Anthropic and OpenAI's approaches to AI safety.
```

```
Summarize what I know about GPT-4o.
```

```
What does Dapr's pub/sub streaming subscription type do?
```

---

## Maintenance

```
Lint the wiki
```

Claude will find contradictions, stale claims, orphan pages, and suggest new sources to ingest.

---

See `CLAUDE.md` for the full schema and operation details.
