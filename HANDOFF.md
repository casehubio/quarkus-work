# quarkus-work — Session Handover
**Date:** 2026-04-29

## Project Status

637 runtime tests passing. Epic #106 (Multi-Instance WorkItems) closed and pushed. All dependents (quarkus-work-reports, quarkus-work-notifications) compile clean.

## What Was Done This Session

### Epic #106 — Multi-Instance WorkItems — CLOSED

Full M-of-N parallel WorkItem implementation. 18 tasks, 26 commits.

**Key design decisions (see `docs/superpowers/specs/2026-04-28-multi-instance-workitems-design.md`):**
- LAYERING.md boundary rule refined: test is "does it impose ordering or require external context?" — not "does it touch another WorkItem." Group policies (M-of-N, cascade cancel) are quarkus-work primitives.
- `Stage.requiredItemIds` in CaseHub: correct for heterogeneous named plan items only. Multi-instance → CaseHub observes parent COMPLETED event, treats group as atomic.
- OCC counting: `@Version` on `WorkItemSpawnGroup` + `policyTriggered` flag (exactly-once). Two-bean pattern: `MultiInstanceCoordinator` (@ObservesAsync, no @Transactional) + `MultiInstanceGroupPolicy` (@Transactional). Events fired outside transaction boundary.
- Threaded inbox: `GET /workitems/inbox` always returns roots (`parentId IS NULL`) with aggregate stats. Coordinator parents surface via descendant visibility.

**New classes:** `runtime/src/main/java/io/quarkiverse/work/runtime/multiinstance/` — MultiInstanceSpawnService, MultiInstanceGroupPolicy, MultiInstanceCoordinator, Pool/Explicit/RoundRobin/CompositeAssignmentStrategy.

**New API:** `GET /workitems/{id}/instances`, `GET /workitems/inbox` (threaded).

**Key gotchas discovered (captured in garden + CLAUDE.md):**
- Quarkus/Narayana wraps OCC as RollbackException during JTA commit — catch `Exception` broadly in retry loops
- `fireAsync()` inside @Transactional fires immediately, not on commit — fire from outside the transaction
- Subagent test reports are unreliable — always verify independently

### casehub-parent conventions updated
- `docs/conventions/atomic-threshold-counters.md` — OCC + policyTriggered pattern
- `docs/PLATFORM.md` — protocol self-evolution rule; M-of-N in capability ownership table

## Open / Next

| Priority | What |
|---|---|
| 1 | #93 Distributed SSE — Redis pub/sub for WorkItemEventBroadcaster |

## Key References

- Spec: `docs/superpowers/specs/2026-04-28-multi-instance-workitems-design.md`
- LAYERING.md (refined): `docs/architecture/LAYERING.md`
- Blog: `blog/2026-04-29-mdp01-m-of-n-rule-rewriting.md`
- Garden entries: GE-20260429-603196 (Narayana OCC), GE-20260429-f61708 (fireAsync), GE-20260429-63a862 (subagent reports), GE-20260429-da95ec (two-bean pattern), GE-20260429-cd60ee (completeFromSystem)
- Build discipline: `CLAUDE.md` § Build Discipline
