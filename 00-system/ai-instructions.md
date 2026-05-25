# AI Agent Instructions

## Reading Order

When starting a new task:

1. Read `README.md` (this vault's root)
2. Read this file (`00-system/ai-instructions.md`)
3. Read `00-system/workspace-map.md` to locate files
4. Read the vault section relevant to your task
5. Only go to the codebase if the vault doesn't answer your question

## Core Principles

1. **Vault first, codebase second.** The vault provides fast context. Use it before reading source files.
2. **Codebase is truth.** If vault and code disagree, trust the code. Report the discrepancy.
3. **Don't guess from vault alone.** For implementation details, always verify against the actual code.
4. **Respect open questions.** Items marked as open/pending are not decided — don't treat them as facts.
5. **Update as you go.** If you make architectural changes, update the relevant vault files.

## What the Vault Contains

- System architecture and service relationships
- Per-service summaries (endpoints, domain, status)
- Code patterns and conventions
- Technology choices with rationale
- Infrastructure configuration overview
- Architecture Decision Records (ADRs)
- Roadmap and completion status

## What the Vault Does NOT Contain

- Implementation details (read the code)
- Line-by-line code documentation
- Temporary debugging notes
- Git history or changelog
- Test data or fixtures

## When to Update the Vault

Update when:
- A new service, endpoint, or domain behavior is added
- An architecture decision is made or changed
- A technology is added, replaced, or removed
- The vault contradicts the codebase

Do NOT update when:
- Small refactors that don't change behavior
- Experimental changes not yet confirmed
- Temporary debugging or analysis work

## Reporting Format

After completing vault-related work, report:

```md
## Files Read
- List of vault/codebase files consulted

## Files Updated
- List of vault files changed (or "None")

## Key Findings
- Important discoveries

## Vault / Codebase Consistency
- Any discrepancies found

## Open Questions
- Unresolved items
```
