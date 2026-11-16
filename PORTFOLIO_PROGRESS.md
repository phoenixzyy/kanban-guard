# kanban-guard progress

## Local verification and Fork — 2026-11-17 (Asia/Shanghai)

- Target account: phoenixzyy, ID 7067843; normal public Fork created from
  dkorecko/Ticky as https://github.com/phoenixzyy/kanban-guard after local validation.
  GitHub displays the upstream Fork relationship; all branches were requested.
- Fixed upstream baseline: ac9bfc77272c91539c05662c04165941fbb7b34b.
  Full history retained. MIT LICENSE.txt retained verbatim, including upstream's
  unfilled copyright placeholders. No invented attribution or experience.
- Inspected contribution instructions, source, tests, workflow and recent upstream
  changes. No repository AGENTS.md or SKILL.md found; workspace .agents is empty.
- Baseline: 258 tests passed, 0 failed/skipped. Initial parallel MSBuild failed
  because named-pipe sockets were unavailable; single-node compilation worked.
  This environment issue is separate from the reproduced business defects.
- Four new regression cases failed on original production code: previous/next
  moves bypassed full-column capacity and failed to repair card order.
- Fix: share existing general-move rules with adjacent moves, accept only -1/+1,
  return success/failure and show failure feedback in the dialog.
- Added 13 cases. CardService focused suite: 51 passed. Final full Release suite:
  271 passed, 0 failed/skipped. Existing obsolete/nullable compilation warnings
  remain; the test SDK also emits a VSTEST_ARTIFACTS_DIRECTORY notice.
- .NET SDK 10.0.404 on Linux; NUnit and SQLite. npm ci, CSS compilation and
  committed CSS consistency check passed with Node 24.22.1/npm 11.9.0.
  CI retains upstream Node 22. git diff --check passed.
- CI supports main pushes, PRs and manual runs; container publishing is guarded
  to dkorecko/Ticky only. No deployment, service, purchase, upstream contact or PR.
- English PORTFOLIO.md and Chinese LEARNING.zh-CN.md explain scope, provenance,
  reproduction, design, interview questions and exercises. README explicitly
  distinguishes the upstream author's biography from this portfolio's owner.
- Limits: no concurrent-capacity guarantee, MySQL integration, full-app startup,
  browser click-through, SMTP, containers or load verification. Razor compiles;
  visual feedback still needs local manual/browser verification.
- Logs retained in the local workspace: ticky-baseline.log,
  ticky-baseline-single-node.log, ticky-red.log, ticky-focused.log,
  ticky-final.log and ticky-npm-baseline.log. No private test data uploaded.
- Publication of the verified change and remote CI verification are pending.

Other portfolio projects are unchanged. The workspace-wide progress file keeps
all earlier records; this repository checkpoint only covers kanban-guard.

## Publication checkpoint

- Published feature commit: e5ba9e4db86eecb204dccd887f7d30234d4a24cf.
- Fetched the public remote and verified tree 357e87da0f3802aa65c817538b0f86e61bc3828a
  exactly matches tested local commit adb8f01d80ef9baeb261351630818b3742006549.
- Parent is the pinned upstream baseline; no force update was used.
- Fork Actions enabled after publishing the upstream-only container guards.
  This checkpoint triggers the first push CI run; its result is pending verification.
