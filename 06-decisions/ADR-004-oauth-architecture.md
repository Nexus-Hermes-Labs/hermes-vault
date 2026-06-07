# ADR-004: Google OAuth2 Login Architecture

**Status**: Accepted (June 2026)
**Service**: auth-service

## Context

auth-service supported only email + password auth. We needed social login,
starting with Google, without breaking the existing bearer-token-in-body model
(every other auth endpoint returns `{access_token, refresh_token}` in the JSON
body; the SPA stores them client-side).

Three viable shapes existed for delivering tokens after the provider redirect:
- **A** — frontend owns the `redirect_uri`; backend callback returns JSON tokens.
- **B** — backend owns the `redirect_uri`; sets httpOnly cookies + redirects (BFF).
- **C** — backend owns the callback; redirects with tokens in the URL fragment.

## Decision

**Architecture A.** The Google `redirect_uri` points at a **frontend** route. The
SPA reads `code` + `state` and POSTs them to `POST /api/v1/auth/oauth/google/callback`,
which returns the same `AuthResponse` JSON as `/login`.

- `GET /api/v1/auth/oauth/google` → `{ authorize_url }` (SPA navigates there).
- **CSRF + PKCE**: server generates a random `state` and a PKCE S256 pair. The
  `code_verifier` is stored in Redis under `oauth:state:{state}` (TTL 600s,
  single-use via `GETDEL`) and never reaches the browser.
- **Account linking**: on a provider-verified email, auto-link to an existing
  credential (insert `oauth_accounts` row) and log in. New emails create a
  verified, OAuth-only credential (random Argon2 hash) and emit the same
  `user.created` outbox event as registration so user-service provisions a profile.
- Unverified provider emails are rejected (403).

Provider access is an outbound port (`GoogleOAuthClient`) so a deterministic fake
is injected in tests; the provider is wired unconditionally and reports
`ProviderNotConfigured` (503) at request time when credentials are absent.

## Rationale

- A is consistent with the existing bearer-in-body flow; B would be a half
  migration to cookies for one endpoint only; C leaks long-lived refresh tokens
  into URL history.
- PKCE adds value despite a confidential client because the `code` transits the
  browser; the verifier stays server-side in Redis.

## Consequences

- New `oauth_accounts` table (`UNIQUE(provider, provider_user_id)`); a
  concurrent first-login race is caught on that constraint and recovered by
  re-fetching the winning link.
- Tokens still reach JavaScript (same XSS surface as `/login`); a future BFF
  migration could move all auth to httpOnly cookies.
- Username for new OAuth users is synthesized (email local-part + random suffix);
  user-service owns final uniqueness. A future "choose username" step can refine it.
- `link` / `unlink` management endpoints are deferred — login only for now.

## Related

- [[ADR-003-transactional-outbox]] — new OAuth users emit `user.created` via the outbox
