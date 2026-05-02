---
type: topic
tags: [knowledge-management, wikis, llm, maintenance, community]
created: 2026-05-02
updated: 2026-05-02
sources: [wiki/sources/llm-wiki-pattern]
---

# Wiki Maintenance

The problem of keeping a wiki current, consistent, and growing — and why it is the central challenge of any knowledge base project.

## Why wikis fail

Humans abandon wikis because the **maintenance burden grows faster than the perceived value**. The work that makes a wiki useful — updating cross-references when a fact changes, noting when one page contradicts another, creating pages for concepts mentioned in passing, removing stale claims — is tedious, invisible, and never urgent. It always loses to other priorities.

Community wikis (e.g. Tolkien Gateway, fandom wikis) succeed only because they distribute the burden across hundreds of motivated volunteers. A solo researcher cannot replicate that. Corporate internal wikis are famously useless within months of launch for exactly this reason.

## The LLM solution

[[wiki/sources/llm-wiki-pattern]] frames this as the core insight:

> "LLMs don't get bored, don't forget to update a cross-reference, and can touch 15 files in one pass. The wiki stays maintained because the cost of maintenance is near zero."

An LLM can perform a full ingest — reading a source, writing a summary, updating 10–15 existing pages, flagging contradictions, updating the index and log — in a single turn. The marginal cost per source stays flat as the wiki grows.

## What "maintenance" means concretely

- Updating entity/concept pages when a new source adds information
- Flagging contradictions between pages (rather than silently resolving them)
- Adding cross-references between newly related pages
- Marking stale claims when a newer source supersedes them
- Creating pages for concepts mentioned but not yet given their own page
- Running periodic lint passes to catch drift

## Remaining risks

Even with an LLM, maintenance can fail if:
- The schema drifts and the LLM ignores conventions from earlier sessions (mitigated by a good `CLAUDE.md`)
- Ingests become too infrequent and the wiki falls behind the raw sources
- The index grows large enough that the LLM misses relevant pages (signals the need for a search tool like `qmd`)

## See also

- [[wiki/concepts/persistent-knowledge-base]] — the structure that makes maintenance worthwhile
- [[wiki/concepts/memex]] — Bush's vision; wiki maintenance was the gap he couldn't fill
- [[wiki/entities/vannevar-bush]] — originated the framing of knowledge as a linkage problem
