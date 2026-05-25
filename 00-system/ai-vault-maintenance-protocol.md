# Vault Maintenance Protocol

## When to Update

### Always Update

- New service added or existing service status changes
- New API endpoint added to an active service
- Architecture decision made or reversed (add/update ADR)
- Technology added, replaced, or removed
- Domain event contract changed (subject, payload shape)
- Database schema change with architectural impact
- Communication pattern changed (new gRPC call, new NATS stream)
- Vault content found to contradict codebase

### Never Update

- Cosmetic refactors (renames, formatting)
- Bug fixes that don't change architecture
- Test additions that don't change behavior
- Experimental branches not merged to main
- Temporary debugging artifacts
- Performance tweaks without architectural change

## How to Update

1. **Identify the affected vault file(s)** — use the workspace map
2. **Read the current vault content** before editing
3. **Verify against codebase** — don't propagate stale information
4. **Update in place** — edit existing files rather than creating new ones
5. **If a new ADR is needed**, create it in `06-decisions/` with the next number
6. **Update cross-references** — if a service map entry changes, check related files

## Consistency Checks

When working in the vault, verify:

- Service ports match `hermes-be/docker-compose.yml`
- Endpoint lists match actual route definitions
- Technology versions match `Cargo.toml` / `package.json`
- gRPC relationships match actual `proto/` and client code
- NATS subjects match actual publisher/consumer code
- Service status matches actual implementation state

## Open Questions

Mark unresolved items with:

```md
> **Open Question:** [description of what's undecided]
```

Convert to resolved statements only when the decision is confirmed and implemented.

## Staleness Risk

Files most likely to go stale (check these first):

1. `01-architecture/roadmap-status.md` — service completion percentages
2. `02-services/*.md` — endpoint lists, feature status
3. `01-architecture/service-map.md` — port assignments, service relationships
4. `03-domain/event-contracts.md` — NATS subjects and payloads
