# casehub-work — Session Handover
**Date:** 2026-05-01

## Project Status

~1167 tests passing across all modules. Working tree clean after commit. All pushed.

## What Was Done This Session

### Refinement epic (#147) — closed
- #151 ✅ Flyway convention documented; V3000 collision fixed (issue-tracker → V5000)
- #150 ✅ `WorkItemEventBroadcaster` + `WorkItemQueueEventBroadcaster` extracted as SPIs; `@DefaultBean` local impls; CDI wiring test
- #149 ✅ `ExpiryLifecycleService` extracted from job classes; 11 unit tests
- #148 ✅ DESIGN.md split → `ARCHITECTURE.md` + lean tracker
- #152 deferred (low priority, parked)

### Distributed SSE (#93, #155) — complete
- `casehub-work-postgres-broadcaster` — `PostgresWorkItemEventBroadcaster` via PostgreSQL LISTEN/NOTIFY; `WorkItemEventPayload` wire DTO; `fromWire()` added to runtime; 22 tests
- `casehub-work-queues-postgres-broadcaster` — `PostgresWorkItemQueueEventBroadcaster`; plain record serialization; UserTransaction in IT tests; 13 tests
- **Key gotcha:** `PgPool.getConnection()` returns `Uni<SqlConnection>` (Mutiny wrapper); must `.getDelegate()` + `PgConnection.newInstance()` to access `notificationHandler()`
- **Key gotcha:** `@Observes(during = AFTER_SUCCESS)` silently skips if no JTA transaction is active — tests need explicit `UserTransaction.begin()/commit()`
- Epic #92 updated; #97 updated with 4 concrete blockers tracking Qhorus issues

### GitHub-first experience (#156, #157) — phase 1 done
- `IssueTrackerProvider.syncToIssue()` default no-op added to SPI
- `GitHubIssueTrackerProvider` syncs priority/category/status labels + WorkItem labels + issue state on every lifecycle event; labels auto-created per repo
- 46 tests (19 new label builder unit tests + 4 service sync tests)
- Epic #156 open with phases 2 (webhooks), 3 (identity mapping), 4 (two-way labels)

### Other
- Flaky `WorkItemGroupLifecycleEventTest` fixed — inter-test `@ObservesAsync` contamination (filter by parentId) + `Awaitility.during()` stability window
- MongoDB persistence module audited — fully implemented (27 tests); ARCHITECTURE.md corrected (was wrongly marked future)
- `casehub-ledger` configurable datasource documented in CLAUDE.md + ARCHITECTURE.md
- #134, #147, #148–#151, #154, #155, #157 all closed

## Open / Next

| Priority | What |
|---|---|
| 1 | #97 — wait for qhorus#131 (Channel abstraction) + qhorus#132 (delivery guarantees) to close, then build `casehub-work-qhorus` |
| 2 | #156 phase 2 — incoming GitHub webhooks (WorkItem driven from GitHub) |
| 3 | #117 — RoundRobinStrategy (small, standalone) |
| — | #152 — examples split (deferred, low priority) |

## Key References

- New modules: `casehub-work-postgres-broadcaster/`, `casehub-work-queues-postgres-broadcaster/`
- Broadcaster SPI: `runtime/src/main/java/io/casehub/work/runtime/event/WorkItemEventBroadcaster.java`
- GitHub sync: `casehub-work-issue-tracker/src/main/java/io/casehub/work/issuetracker/`
- Blog: `blog/2026-05-01-mdp02-delegates-transactions-parallel-universe.md`
- Previous session: `git show HEAD~25:HANDOFF.md`
