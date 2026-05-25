# Frontend Overview

React 19 + Vite SPA, served via Docker container on `hermes-network`. Traefik routes `/` (lowest priority) to it.

## Stack

| Technology | Purpose |
|---|---|
| React 19 | UI framework |
| Vite | Dev server + bundler |
| TypeScript | Type safety |
| TanStack Router | Type-safe client-side routing |
| TanStack Query | Server state, caching, revalidation |
| Zustand | Client state (auth user, UI) |
| Zod | Runtime schema validation (forms + API responses) |
| react-hook-form | Uncontrolled forms with Zod validation |
| Tailwind CSS v4 | Utility-first styling |
| Radix UI | Accessible unstyled primitives (Dialog, Popover, etc.) |
| shadcn/ui | Copy-pasted Tailwind-styled Radix components in `src/components/ui/` |

## Development

- **Dev container**: `hermes-fe/Dockerfile.dev` — node:22-alpine, source-mounted for HMR
- **Vite config**: `host: 0.0.0.0`, `hmr.clientPort: 80` (HMR through Traefik)
- **Port**: 3001 inside container, accessed via Traefik on port 80
- **Production build**: `hermes-fe/Dockerfile` — node build → serve@14

## Patterns

- Components don't fetch directly — use `useQuery()` / `useMutation()` from TanStack Query
- Mutations invalidate query keys on success for automatic cache refresh
- Zustand only for non-server state (auth token, UI preferences)
- All forms use react-hook-form + Zod resolver

## Current State

Frontend development is less mature than the backend. The React app structure exists but most views are still being built alongside the backend API completion.

> **Open Question:** Detailed frontend feature completion status needs verification against the actual `hermes-fe/src/` structure.
