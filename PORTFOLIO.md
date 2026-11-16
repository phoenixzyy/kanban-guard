# kanban-guard: consistent adjacent-column moves

## Provenance

Derived from [dkorecko/Ticky](https://github.com/dkorecko/Ticky), pinned to
`ac9bfc77272c91539c05662c04165941fbb7b34b`. The existing ASP.NET Core Blazor app,
Kanban features, EF Core model, identity, API and migrations are upstream work.
This contribution was prepared with Codex assistance for a portfolio; independent
human review is not claimed. No experience or proficiency is inferred from it.

`LICENSE.txt` is MIT and retained verbatim. Its upstream copyright line is the
literal `Copyright (c) [year] [fullname]`; we do not invent replacement names or
years. The upstream README's first-person biography remains attributed to its
original author. Full Git history and normal Fork provenance must be preserved.
Publication and remote validation status are tracked in PORTFOLIO_PROGRESS.md.

Recent upstream changes reviewed include #210 (email encoding), #208 (dependency
updates), #198 (repeat-column board boundaries), #196 (card access), and #192
(API/bearer authentication). Those capabilities and fixes are not this project's
contribution. No upstream issue, PR or message was created.

## Reproduced behavior and change

Set an adjacent column's MaxCards to 1 and fill it. The card dialog's previous/next
column action called `MoveToAdjacentColumnAsync`, which directly assigned the
column and logged a move. It bypassed the capacity check already present in
`MoveToColumnAsync`. In SQLite service tests, both directions moved a second card
into that full column and logged the forbidden move. Successful adjacent moves
also retained their source index instead of appending and repairing both columns.

Four new regression cases (full/ordering × previous/next) failed on unchanged
production code. The implementation now shares the existing capacity, ordering,
activity and save logic between the two service entry points, in the same
DbContext. The general movement algorithm is reused, not newly invented here.

Adjacent moves now:

- accept only offsets -1 and +1;
- preserve board/project-member access checks and same-board target selection;
- reject full columns before any card/index/activity change;
- append valid moves and normalize both column index sequences;
- return `Task<bool>` so the dialog can show a failure notification and refresh.

Existing callers that only await the method still compile; consumers explicitly
depending on its old method signature should recompile. The message deliberately
covers full, unavailable and inaccessible moves without claiming to know which
reason applies. Normal `MoveToColumnAsync` semantics remain unchanged.

## Verification

See the progress checkpoint for exact counts and remote run links. Local evidence
uses Linux, .NET SDK 10.0.404, NUnit and the upstream in-memory SQLite test fixture.
No synthetic test data is sent externally. The local environment disallows
MSBuild's multi-process named pipes, so the baseline used single-node compilation:

```sh
dotnet test -c Release -m:1 -p:UseSharedCompilation=false
dotnet test -c Release -m:1 -p:UseSharedCompilation=false --no-restore \
  --filter 'FullyQualifiedName~CardServiceTest'
cd Ticky.Web
npm ci
npm run build:css
git diff --exit-code -- wwwroot/css/app.css
```

The frontend baseline used Node 24.22.1/npm 11.9.0; CI keeps upstream Node 22.
The lockfile is unchanged. CI runs restore, Release build, tests and CSS
consistency checks on pushes/PRs, with a manual trigger. Container publishing
jobs are restricted to the upstream repository and do not execute in this fork.

The four initial red tests asserted stored state without requiring a new return
value. The final tests additionally check false on rejection, true when capacity
is available (including 0 = unlimited), non-adjacent offsets, missing cards,
missing neighbors, outsider access, project-member access and activity snapshots.

## Limits

This fixes inconsistent entry-point behavior, **not concurrent capacity races**.
The existing read-count-then-save policy is not a database-enforced invariant:
parallel moves, inserts, repeats or imports can still require separate concurrency
work. No transaction locking redesign or migration was introduced. All stored
cards count toward capacity, matching the general service; filtering/snoozing
semantics are not redesigned.

SQLite tests execute real EF Core service code, but are not MySQL integration or
browser E2E tests. Full application startup against MySQL, click-through behavior,
SMTP, container runtime, multi-instance concurrency and load performance are not
claimed as verified. The dialog change is compilation-checked; its visual rendering
needs a future local manual/browser check. No public deployment is part of this work.
