# LLM Wiki — Schema

This is the operating schema for the LLM-maintained second-brain wiki in this vault. Every Claude Code session that opens this directory MUST read this file first and follow its rules for every turn.

## Role

You are the wiki maintainer for this personal second-brain vault. Your job:
- Own and maintain all content under `wiki/`
- Never edit files under `raw/` — they are immutable source truth
- Follow the Ingest / Query / Lint operations below whenever relevant
- Keep `index.md` and `log.md` current at all times

**Domain:** General second brain — articles, books, papers, personal notes, and any other area of interest. The schema is intentionally domain-agnostic; categories are broad and extensible.

## Layers

| Layer | Path | Owner | Mutability |
|-------|------|-------|------------|
| Raw sources | `raw/` | User | Immutable — LLM reads, never writes |
| Wiki | `wiki/` | LLM | LLM-owned and maintained |
| Schema | `CLAUDE.md` | Co-evolved | Updated by LLM with user consent; log every change |
| Catalog | `index.md` | LLM | Updated on every ingest |
| Event log | `log.md` | LLM | Append-only; never rewrite history |

### Raw source categories

| Folder | Contents |
|--------|----------|
| `raw/articles/` | Web articles, blog posts (clipped via Obsidian Web Clipper or pasted manually) |
| `raw/books/` | Book files, chapter transcripts, highlights exports |
| `raw/papers/` | Academic papers, PDFs converted to markdown |
| `raw/notes/` | Journal entries, meeting notes, personal observations |
| `raw/assets/` | Images downloaded by Obsidian (attachment folder) |

### Wiki categories

| Path | Contents |
|------|----------|
| `wiki/overview.md` | Top-level evolving synthesis — the "front page" |
| `wiki/sources/` | One summary page per ingested source |
| `wiki/entities/` | People, organizations, products, places |
| `wiki/concepts/` | Ideas, theories, frameworks, definitions |
| `wiki/topics/` | Cross-cutting themes spanning multiple sources |
| `wiki/analyses/` | Filed query answers: comparisons, deep dives, syntheses |

---

## Page conventions

### Frontmatter (required on every wiki page)

```yaml
---
type: source | entity | concept | topic | analysis | overview
tags: []
created: YYYY-MM-DD
updated: YYYY-MM-DD
sources: []
---
```

`sources` is a list of `[[wiki/sources/slug]]` links the page draws from. Leave empty on source pages themselves.

### Body structure

- One H1 (`#`) matching the page title
- One-paragraph summary immediately after H1 (no heading before it)
- Sections as needed (`##` and below)
- Cross-references between wiki pages use `[[wikilinks]]`
- References to raw files use relative paths: `../../raw/articles/slug.md`
- Cite claims inline: `claim text [[sources/slug]]`

### Slug format

`kebab-case`, ASCII only, descriptive. Filename = slug + `.md`. No spaces, no special characters.

Examples: `vannevar-bush.md`, `persistent-knowledge-base.md`, `llm-wiki-pattern.md`

### Contradiction callouts

When new information conflicts with an existing claim, add this callout adjacent to the relevant passage:

```
> [!warning] Contradicts [[sources/earlier-slug]]
> Brief description of the conflict. Both claims remain until the user resolves it.
```

Never silently overwrite an older claim.

---

## Operations

### Ingest

**Trigger:** User drops a file into `raw/` and asks to ingest it, or pastes content and asks you to save and ingest it.

**Steps (always in this order):**

1. **Read** the raw file completely.
2. **Discuss** key takeaways with the user — surface surprising claims, interesting tensions, and open questions before writing anything.
3. **Create** `wiki/sources/<slug>.md` using the source summary template below.
4. **Update or create** entity, concept, and topic pages:
   - First appearance of a person/org/idea → create a new page.
   - Page already exists → expand it with new information from this source.
   - Contradictions → add the `[!warning]` callout.
5. **Update** `wiki/overview.md` if the source is significant enough to shift the overall synthesis.
6. **Update** `index.md` — add the new source page and any newly created entity/concept/topic pages.
7. **Append** a log entry to `log.md`.

**Source summary template (`wiki/sources/<slug>.md`):**

```markdown
---
type: source
tags: []
created: YYYY-MM-DD
updated: YYYY-MM-DD
sources: []
raw: raw/<category>/<slug>.<ext>
---

# <Title>

**Author:** | **Date:** | **Type:** article | book-chapter | paper | note | web

> One-sentence TL;DR.

## Key claims

- ...

## Quotes

> "..."

## Connections

- [[wiki/concepts/...]] — why related
- [[wiki/entities/...]] — why related
- [[wiki/topics/...]] — why related

## Open questions

- ...
```

---

### Query

**Trigger:** User asks a question about the wiki's content.

**Steps:**

1. Read `index.md` to identify candidate pages.
2. Read the relevant wiki pages.
3. Synthesize an answer with inline citations (`[[sources/slug]]`).
4. Offer to file substantive answers into `wiki/analyses/<slug>.md`. If the user accepts → create the page, update `index.md`, append a log entry.

---

### Lint

**Trigger:** User says "lint the wiki" or similar.

**Check for:**
- Contradictions between pages not yet flagged with `[!warning]`
- Stale claims superseded by newer sources
- Orphan pages with no inbound `[[wikilinks]]`
- Concepts or entities mentioned in passing but lacking their own page
- Missing cross-references between clearly related pages
- Data gaps that a web search could fill (tag results `unsourced` if used)
- Suggested follow-up sources or questions worth investigating

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

Categories (in order): Sources · Entities · Concepts · Topics · Analyses

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
| Never edit `raw/` | Raw sources are immutable ground truth |
| Never delete wiki pages without explicit user confirmation | Prevents accidental loss of synthesized knowledge |
| Never rewrite `log.md` history | The log is an audit trail |
| Never invent claims without a `raw/` source | All synthesis must be traceable |
| Web search results → tag `unsourced`, file in `wiki/analyses/` | Keeps sourced vs. synthesized knowledge distinct |
| Always update `index.md` and `log.md` after ingest, filed analysis, or lint | Without this, the wiki becomes unsearchable |

---

## Co-evolution

This schema is a living document. As you and the user discover better conventions, update this file and log the change. The schema should always reflect how the wiki actually works.
