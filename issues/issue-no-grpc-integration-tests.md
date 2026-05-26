---
title: "Issue: No gRPC Integration Tests"
tags: [issue, testing, quality]
source: raw/docs/ROADMAP.md
date: 2026-05-26
status: active
---

# Issue: No gRPC Integration Tests

**Severity**: Medium — quality risk

Cross-service gRPC communication (auth→user, guild→channel, guild→user, channel→guild) is untested end-to-end. Individual service tests exist but don't verify the full inter-service flow.

## Sources

- [[2026-05-26-roadmap-doc]]

## Related

- [[auth-service]], [[user-service]], [[guild-service]], [[channel-service]]
