# Hermes Vault — LLM Wiki Rulebook

## Purpose

Persistent knowledge archive for the **Hermes** project — a real-time communication platform with AI-powered translation, built as Rust microservices. This vault stores architecture decisions, entity references, concept definitions, issue records, and synthesis documents so that AI agents and humans can understand the project without re-reading the entire codebase.

## Language

All pages are written in **English**.

## Naming Convention

- File names: **kebab-case** (e.g., `auth-service.md`, `transactional-outbox.md`)
- No spaces, no uppercase in filenames
- Dates in filenames: `YYYY-MM-DD-<slug>.md`

## Page Format

Every page follows this structure:

```markdown
---
title: <Page Title>
tags: [tag1, tag2]
source: <origin file or URL>
date: YYYY-MM-DD
status: active | draft | archived
---

# <Page Title>

<Content>

## Sources

- [[source-page]] — context

## Related

- [[related-page-1]]
- [[related-page-2]]
```

## Directory Structure

```
hermes-vault/
├── 00-system/          # AI instructions, workspace map (pre-existing)
├── 01-architecture/    # Architecture summaries (pre-existing)
├── 02-services/        # Per-service summaries (pre-existing)
├── 03-domain/          # Domain patterns (pre-existing)
├── 04-infrastructure/  # Infra docs (pre-existing)
├── 05-frontend/        # Frontend docs (pre-existing)
├── 06-decisions/       # ADRs (pre-existing)
├── raw/                # Immutable source material (symlinks only)
│   ├── codebase/       # Symlinks to key codebase files
│   └── docs/           # Symlinks to documentation files
├── sources/            # Ingested source summaries
│   └── codebase/       # One page per ingested source
├── entities/           # Services, files, functions, APIs, people
├── concepts/           # Architectural patterns, design principles
├── decisions/          # Atomic decision records (wiki-style)
├── issues/             # Problems found, root causes, fixes
├── syntheses/          # Cross-cutting high-level summaries
├── archive/            # Retired/superseded pages (never delete)
├── index.md            # Master index of all pages by category
└── log.md              # Chronological activity log
```

## Workflows

### Ingest Workflow

When processing a new source file:

1. **Link** the source file into `raw/codebase/` or `raw/docs/` as a symlink
2. **Read** the file, extract the main topic and key facts
3. **Create** `sources/codebase/YYYY-MM-DD-<slug>.md` with:
   - YAML frontmatter (title, tags, source, date, status)
   - Goal, what was done, files changed, decisions, issues, open threads
4. **Create or update** entity pages in `entities/` for every named entity (service, file, function, API)
5. **Create** decision pages in `decisions/` for every architectural/product decision found
6. **Create** issue pages in `issues/` for every problem/fix found (root cause + fix + related files)
7. **Create or update** concept pages in `concepts/` for every abstract pattern or principle
8. **Cross-link** all pages bidirectionally using `[[wiki-links]]`
9. **Append** to `log.md`: `## [YYYY-MM-DD] ingest | <slug>` with list of touched files
10. **Update** `index.md` with new page references

### Query Workflow

When answering a question about Hermes:

1. Check `index.md` for relevant categories
2. Read relevant entity/concept/decision pages
3. If vault doesn't answer, go to `raw/` sources or the live codebase
4. If new knowledge is found, run the Ingest Workflow for it

### Lint Workflow

Periodic quality check:

1. Find **orphan pages** (no incoming links)
2. Find **broken links** (`[[target]]` where target doesn't exist)
3. Find **missing cross-references** (pages that should link to each other but don't)
4. Find **unsourced claims** (facts without a `## Sources` reference)
5. Find **contradictions** between pages
6. Write findings to `lint-report.md`

## Hard Rules

1. **`raw/` is immutable.** Never write to `raw/`. Only add symlinks. Read-only source of truth.
2. **No unsourced claims.** Every factual statement must reference a source in `## Sources`.
3. **Never delete.** Move retired pages to `archive/` with `status: archived` in frontmatter.
4. **Contradictions are explicit.** If two sources disagree, add a `## CONTRADICTION` section explaining both positions. Do not silently pick one.
5. **Codebase wins.** If vault and live code disagree, the code is truth. Update the vault.
6. **Atomic pages.** One concept, one entity, one decision per page. Cross-link liberally.
7. **Bidirectional links.** If page A links to page B, page B should link back to page A.
