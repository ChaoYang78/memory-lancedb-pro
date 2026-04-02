# PATCHES.md

This file exists only for the `memory-lancedb-pro` plugin repository.
It documents the local long-term maintenance state of this plugin, so a future engineer can understand what is running, what is patched locally, and how to evaluate future upstream updates.

## What this file is for

Use this file to answer these questions quickly:

- Which branch is currently intended for live use?
- What is the last known-good rollback point?
- Which local patches still exist outside upstream?
- Which upstream PRs correspond to those patches?
- Before upgrading from upstream, what should be checked first?

This file is about the plugin repository only.
It is not an agent/workspace runbook.

## Branch roles

### `stone/main`
Stable local baseline branch.
Use this as the conservative rollback anchor.

- Baseline commit recorded during stabilization: `66c0838`
- Purpose: known-good local maintenance line before switching live runtime to the integrated upstream branch

### `stone/upstream-integration-2026-04-02`
One-time upstream integration rehearsal branch.
Used to merge local maintenance state with upstream `master` in isolation, resolve conflicts, and validate before live adoption.

- Integration commit: `8a02c81`
- Purpose: rehearsal/integration branch, not the long-term live branch

### `stone/upstream-live`
Current live runtime branch for this plugin.
This is the branch intended to represent the currently adopted upstream-integrated local runtime.

- Live cutover date: `2026-04-02` (Asia/Shanghai)
- Current recorded live commit at cutover: `8a02c81`
- Later documentation commit: `b54c7d3`

## Current live state

At the time this file was last updated, the intended live branch/line for this plugin is:

- Running branch: `stone/upstream-live`
- Latest recorded commit in that line: `b54c7d3`
- Integrated runtime cutover commit: `8a02c81`
- Previous stable rollback anchor: `stone/main` @ `66c0838`

## Local patches currently expected to exist

### 1. Rerank retrieval config env resolution
- Scope: retrieval rerank-related config values in `index.ts`
- Why it exists: upstream currently does not resolve env vars for rerank config values, which breaks configurations stored as environment references
- Upstream PR: `#414`
- PR title: `fix: resolve env vars in retrieval rerank config`
- Expected future action: remove/reduce this local patch only after verifying the upstream branch has absorbed equivalent behavior

### 2. OpenClaw 2026.4.1 memory runtime registration
- Scope: `api.registerMemoryRuntime(...)` compatibility adapter in `index.ts`
- Why it exists: OpenClaw `2026.4.1+` expects memory plugins to explicitly register a memory runtime. Without this, the plugin may load and register tools but still appear unavailable as the active system memory backend.
- Upstream PR: `#449`
- PR title: `fix: register memory runtime for OpenClaw 2026.4.1`
- Expected future action: remove/reduce this local patch only after verifying the upstream branch has absorbed equivalent behavior

## Validation already performed for live cutover

The following checks were completed when switching to the upstream-integrated live branch:

- gateway reload succeeded
- `memory_recall` succeeded
- `memory_store` succeeded
- stored verification token was recalled successfully
- `memory_forget` succeeded
- `openclaw doctor --non-interactive` reported plugin load with `Errors: 0`

These checks establish that the plugin was functioning after the cutover, not just compiling.

## How a future engineer should use this file

When investigating or upgrading this plugin, use this order:

1. Check which branch is currently intended to be live
   - normally `stone/upstream-live`
2. Check the rollback anchor
   - `stone/main` @ `66c0838`
3. Check whether upstream has already merged the two patch lines
   - PR `#414`
   - PR `#449`
4. If upstream has moved forward, compare local `index.ts` behavior before dropping patches
5. Validate functional behavior, not just Git merge success
   - recall
   - store
   - recall written token
   - forget
   - doctor / plugin error check

## Practical maintenance rule

Do not assume a successful upstream merge means the plugin is safe to switch live.
For this plugin, functional memory verification matters more than a clean Git merge.

## Notes

- This file is for the plugin repository only.
- Keep this file concise and operational.
- Update it whenever the live branch, rollback anchor, or required local patches change.
