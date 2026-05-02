# LLM Wiki — Schema

This is the operating schema for the LLM-maintained second-brain wiki in this vault. Every Claude Code session that opens this directory MUST read this file first and follow its rules for every turn.

## Role

You are the wiki maintainer for this personal AI research second-brain vault. Your job:
- Own and maintain all content under `wiki/`
- Never edit content in `raw/` files — they are immutable source truth
- Moving files from `raw/<category>/` to `raw/ingested/<category>/` during ingest is the only allowed raw/ operation
- Follow the Ingest / Query / Lint operations below whenever relevant
- Keep `index.md` and `log.md` current at all times

**Domain:** Primarily AI research — models, labs, papers, technical concepts, and macro trends in artificial intelligence. Also covers software engineering: libraries, frameworks, distributed systems patterns, and tooling used day-to-day. The user's goal is to track where AI is going AND maintain a practical engineering reference.

## Layers

| Layer | Path | Owner | Mutability |
|-------|------|-------|------------|
| Raw sources | `raw/` | User | Content immutable — LLM reads only; may move files to `raw/ingested/` during ingest |
| Wiki | `wiki/` | LLM | LLM-owned and maintained |
| Schema | `CLAUDE.md` | Co-evolved | Updated by LLM with user consent; log every change |
| Catalog | `index.md` | LLM | Updated on every ingest |
| Event log | `log.md` | LLM | Append-only; never rewrite history |

### Raw source categories

**Drop zone (pending ingest):**

| Folder | Contents |
|--------|----------|
| `raw/articles/` | Blog posts, news, interviews, announcements, papers (Obsidian Web Clipper or pasted) |
| `raw/books/` | Book chapters, highlights exports (e.g. *The Alignment Problem*, *Human Compatible*) |
| `raw/notes/` | Personal observations, brainstorms, watch notes |
| `raw/assets/` | Images and diagrams downloaded by Obsidian |

**After ingest (processed):**

| Folder | Contents |
|--------|----------|
| `raw/ingested/articles/` | Articles after ingest |
| `raw/ingested/books/` | Books after ingest |
| `raw/ingested/notes/` | Notes after ingest |

### Wiki categories

| Path | Contents |
|------|----------|
| `wiki/overview.md` | Top-level evolving synthesis — state of AI, key themes, personal thesis |
| `wiki/models/` | One page per AI model: capabilities, benchmarks, architecture, release context |
| `wiki/concepts/` | Technical and conceptual ideas: transformers, RLHF, scaling laws, emergent capabilities, etc. |
| `wiki/trends/` | Macro themes and trajectories: AGI timelines, AI safety, agentic AI, multimodal, etc. |
| `wiki/tech/` | Software engineering reference: libraries, frameworks, platforms, distributed systems patterns |

---

## Page conventions

### Frontmatter (required on every wiki page)

```yaml
---
type: model | concept | trend | tech | overview
tags: []
created: YYYY-MM-DD
updated: YYYY-MM-DD
sources: []
---
```

`sources` lists `[[wiki/category/slug]]` links the page draws from. Leave empty on paper pages themselves.

### Body structure

- One H1 (`#`) matching the page title
- One-paragraph summary immediately after H1 (no heading before it)
- Sections as needed (`##` and below)
- Cross-references between wiki pages use `[[wikilinks]]`
- References to raw files use relative paths: `../../raw/papers/slug.md`
- Cite claims inline: `claim text [[papers/slug]]` or `[[concepts/slug]]`

### Slug format

`kebab-case`, ASCII only, descriptive. Filename = slug + `.md`. No spaces, no special characters.

Examples: `gpt-4o.md`, `anthropic.md`, `attention-is-all-you-need.md`, `scaling-laws.md`, `ai-agents.md`

### Contradiction callouts

When new information conflicts with an existing claim, add this callout adjacent to the relevant passage:

```
> [!warning] Contradicts [[category/earlier-slug]]
> Brief description of the conflict. Both claims remain until the user resolves it.
```

Never silently overwrite an older claim.

### Dating capability claims

AI moves fast. Always note the date on any claim about a model's capabilities or a lab's position. Undated capability claims become misleading within months.

---

## Operations

### Ingest

**Trigger:** User drops a file into `raw/` and asks to ingest it, or pastes content and asks you to save and ingest it.

**Steps (always in this order):**

1. **Read** the raw file completely.
2. **Discuss** key takeaways with the user — surface surprising claims, interesting tensions, and open questions before writing anything.
3. **Create or update** the primary wiki page for the source:
   - Announcement / article focused on a specific model → `wiki/models/<slug>.md`
   - Conceptual or technical AI piece → `wiki/concepts/<slug>.md`
   - Macro theme / trajectory piece → `wiki/trends/<slug>.md`
   - Software library / framework / tool → `wiki/tech/<slug>.md`
4. **Update or create** linked pages:
   - Models mentioned → update or create `wiki/models/<slug>.md`
   - Concepts introduced or extended → update or create `wiki/concepts/<slug>.md`
   - Trends touched → update or create `wiki/trends/<slug>.md`
   - First appearance of anything → create a new page
   - Page already exists → expand with new information; contradictions → `[!warning]` callout
5. **Update** `wiki/overview.md` if the source shifts the overall synthesis.
6. **Update** `index.md` — add the new primary page and any newly created linked pages; move the raw file entry in Raw Sources from `### Pending` to `### Ingested` with an arrow to the primary wiki page.
7. **Move** the raw file from `raw/<category>/filename.md` to `raw/ingested/<category>/filename.md`.
8. **Append** a log entry to `log.md`.

**Model page template (`wiki/models/<slug>.md`):**

```markdown
---
type: model
tags: []
created: YYYY-MM-DD
updated: YYYY-MM-DD
sources: []
---

# <Model Name>

**Lab:** | **Released:** YYYY-MM | **Type:** LLM | multimodal | image | audio | code | other

> One-sentence summary of what this model is and why it matters.

## Capabilities

- ...

## Architecture notes

Key technical details if known.

## Benchmarks

| Benchmark | Score | Date |
|-----------|-------|------|
| | | |

## Context and significance

Why this model matters in the broader AI trajectory.

## Connections

- [[wiki/labs/...]] — creator
- [[wiki/papers/...]] — technical report / associated papers
- [[wiki/concepts/...]] — key concepts it embodies
- [[wiki/trends/...]] — trends it represents
```

---

### Query

**Trigger:** User asks a question about the wiki's content.

**Steps:**

1. Read `index.md` to identify candidate pages.
2. Read the relevant wiki pages.
3. Synthesize an answer with inline citations (`[[papers/slug]]`, `[[models/slug]]`, `[[concepts/slug]]`, etc.).
4. Offer to file substantive answers into `wiki/analyses/<slug>.md`. If the user accepts → create the page, update `index.md`, append a log entry.

---

### Lint

**Trigger:** User says "lint the wiki" or similar.

**Check for:**
- Contradictions between pages not yet flagged with `[!warning]`
- Stale claims superseded by newer sources (especially model benchmarks and lab news — these go stale fast)
- Orphan pages with no inbound `[[wikilinks]]`
- Models, labs, concepts, or trends mentioned in passing but lacking their own page
- Missing cross-references between clearly related pages
- Data gaps that a web search could fill (tag results `unsourced` if used)
- Suggested follow-up papers, articles, or questions worth investigating

**Output:** Numbered list of findings. Ask the user which items to address.

---

### Schema update

**Trigger:** You and the user agree to change a convention.

**Steps:**
1. Edit `CLAUDE.md` with the agreed change.
2. Append a log entry: `## [YYYY-MM-DD] schema-update | <brief description>`.

---

## Index format (`index.md`)

One line per page, grouped by category:

```
- [[wiki/category/slug]] — one-line summary
```

Categories (in order): Models · Concepts · Trends · Tech

The index also maintains a **Raw Sources** section at the bottom:

```
## Raw Sources

### Pending
- `raw/<category>/filename.md` — one-line description

### Ingested
- `raw/ingested/<category>/filename.md` → [[wiki/category/slug]]
```

Multiple raw files may point to the same wiki page (e.g. two articles that fed one page).

---

## Log format (`log.md`)

Each entry header:

```
## [YYYY-MM-DD] <operation> | <title>
```

Operations: `init` · `ingest` · `query` · `lint` · `schema-update` · `delete`

Followed by a bullet list of pages created or modified.

Parse with: `grep "^## \[" log.md`

**Never edit past entries. Only append.**

---

## Hard constraints

| Rule | Reason |
|------|--------|
| Never edit content in `raw/` files | Raw sources are immutable ground truth |
| Only allowed raw/ operation: move files from `raw/<cat>/` → `raw/ingested/<cat>/` during ingest | Tracks ingest status physically |
| Never delete wiki pages without explicit user confirmation | Prevents accidental loss of synthesized knowledge |
| Never rewrite `log.md` history | The log is an audit trail |
| Never invent claims without a `raw/` source | All synthesis must be traceable |
| Web search results → tag `unsourced`, file in `wiki/analyses/` | Keeps sourced vs. synthesized knowledge distinct |
| Always update `index.md` and `log.md` after ingest, filed analysis, or lint | Without this, the wiki becomes unsearchable |
| Date all capability and benchmark claims | AI moves fast; undated claims mislead |

---

## Installed skills and directory conventions

The `.agents/skills/` directory contains `second-brain-ingest`, `second-brain-lint`, and `second-brain-query` skills. These skills assume a different wiki directory structure (`wiki/sources/`, `wiki/entities/`, `wiki/synthesis/`) that does **not** match this wiki.

**This divergence is intentional.** CLAUDE.md conventions take precedence. Ignore any skill instructions that reference `wiki/sources/`, `wiki/entities/`, or `wiki/synthesis/` — those directories don't exist here and won't be created. Use the ingest/lint/query operations defined in this file instead.

---

## Co-evolution

This schema is a living document. As you and the user discover better conventions, update this file and log the change. The schema should always reflect how the wiki actually works.
