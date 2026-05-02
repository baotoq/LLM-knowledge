# LLM Knowledge Wiki

A personal AI research second-brain maintained by Claude Code. Raw sources live in `raw/`; the synthesized wiki lives in `wiki/`. The operating schema is in `CLAUDE.md`.

## How to use

1. Drop a source into the appropriate `raw/` subfolder — or paste content and ask Claude to save and ingest it.
2. Tell Claude to **ingest** it. It will discuss key takeaways with you, then create/update pages across `wiki/models/`, `wiki/labs/`, `wiki/papers/`, `wiki/concepts/`, and `wiki/trends/`.
3. **Ask questions.** Claude reads `index.md`, drills into relevant pages, and answers with citations.
4. Periodically ask Claude to **lint the wiki** — it will surface contradictions, stale claims, orphan pages, and gaps.

## Structure

```
raw/
  papers/       academic papers (arXiv exports, PDF→markdown)
  articles/     blog posts, news, interviews, announcements
  books/        book chapters, highlights
  notes/        personal observations, brainstorms
  assets/       images and diagrams

wiki/
  overview.md   top-level synthesis and research thesis
  models/       one page per AI model (GPT-4o, Claude 3.5, Llama 3, ...)
  labs/         AI organizations (Anthropic, OpenAI, DeepMind, ...)
  papers/       research paper summaries
  concepts/     technical ideas (transformers, RLHF, scaling laws, ...)
  trends/       macro themes (AGI timelines, AI safety, agentic AI, ...)
  analyses/     personal synthesis, comparisons, filed query answers

index.md        catalog of every wiki page (Claude reads this first on every query)
log.md          append-only event history
CLAUDE.md       operating schema — rules Claude follows
```

---

## Ingesting a source

### Option A — File already in `raw/`

Save the file to the right subfolder first, then use one of these prompts:

```
Ingest raw/papers/attention-is-all-you-need.md
```

```
I added a new paper to raw/papers/ — please ingest raw/papers/scaling-laws-neural-lms.md
```

```
Ingest raw/articles/openai-o3-announcement.md
```

### Option B — Paste content directly

```
Save this to raw/articles/anthropic-interpretability-2025.md and ingest it:

<paste content here>
```

```
Here's a paper I want in the wiki. Save it as raw/papers/gemini-1-5-technical-report.md and ingest it:

<paste content here>
```

### What happens during ingest

Claude will:
1. Read the source fully
2. **Discuss** key takeaways, surprising claims, and open questions with you before writing anything
3. Create the primary wiki page (paper summary, model page, lab page, etc.)
4. Update or create all linked pages (models, labs, concepts, trends mentioned)
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
What are the most important papers I've ingested on transformers?
```

```
Summarize what I know about GPT-4o.
```

Add *"save this as an analysis"* to any answer you want filed permanently in `wiki/analyses/`.

---

## Maintenance

```
Lint the wiki
```

Claude will find contradictions, stale claims, orphan pages, and suggest new sources to ingest.

---

See `CLAUDE.md` for the full schema and operation details.
