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
