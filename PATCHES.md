# PATCHES.md

This branch tracks Stone's long-term maintained local working state for `memory-lancedb-pro`.

## Purpose

Keep a stable, traceable local baseline that can be merged/rebased onto future upstream updates.

## Local patches currently required

### 1. Rerank retrieval config env resolution
- Scope: retrieval rerank-related config values in `index.ts`
- Why: upstream currently does not resolve env vars for rerank config values, which breaks config values that are stored as environment references.
- Upstream PR: `#414`
- PR title: `fix: resolve env vars in retrieval rerank config`
- Status at time of snapshot: OPEN

### 2. OpenClaw 2026.4.1 memory runtime registration
- Scope: `api.registerMemoryRuntime(...)` compatibility adapter in `index.ts`
- Why: OpenClaw `2026.4.1+` expects memory plugins to explicitly register a memory runtime. Without this, the plugin can load and register tools but still appears unavailable as an active system memory backend.
- Upstream PR: `#449`
- PR title: `fix: register memory runtime for OpenClaw 2026.4.1`
- Status at time of snapshot: OPEN

## Current live runtime state

- Live switch date: 2026-04-02 (Asia/Shanghai)
- Running branch: `stone/upstream-live`
- Running commit: `8a02c81`
- Source branch for integration rehearsal: `stone/upstream-integration-2026-04-02`
- Previous stable baseline: `stone/main` @ `66c0838`
- Validation performed after live switch:
  - gateway reloaded successfully
  - `memory_recall` succeeded
  - `memory_store` succeeded
  - stored token was recalled successfully
  - `memory_forget` succeeded
  - `openclaw doctor --non-interactive` reported `Plugins: Loaded: 44 / Errors: 0`

## Maintenance notes

- This branch is for Stone's working local baseline, not for upstream review.
- PR branches can be deleted after merge/closure; this branch should remain as the long-term local reference.
- Before adopting newer upstream versions, compare whether PR #414 and PR #449 have already been merged upstream.
- `stone/upstream-live` is the active runtime branch after the 2026-04-02 cutover.
