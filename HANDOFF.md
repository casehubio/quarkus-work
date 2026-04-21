# Quarkus WorkItems — Session Handover
**Date:** 2026-04-21

## Project Status

525 runtime + 9 api = 534 tests, 0 failures. Three epics closed this session; two new modules shipped.

| Module | Tests |
|---|---|
| quarkus-workitems-api | 9 |
| runtime | 525 |
| quarkus-workitems-filter-registry | 40 |
| quarkus-workitems-ai | 8 |
| (others unchanged from previous handover) | — |

## What Was Built This Session

- **Epic #98 — Form Schema** (#107 ✅ #108 ✅): `WorkItemFormSchema` entity + CRUD, payload/resolution validation via networknt JSON Schema
- **Epic #99 — Audit History Query API** (#109 ✅ #110 ✅ #111 ✅): `GET /audit` with filters + pagination, SLA breach report, actor performance summary
- **Epic #100 — AI-Native, feature 1** (#112 ✅ #113 ✅ #114 ✅): `confidenceScore` on WorkItem + V13, `quarkus-workitems-filter-registry` (FilterAction SPI + JEXL engine + permanent/dynamic registry), `quarkus-workitems-ai` (LowConfidenceFilterProducer)
- **Epic #100 / #102 — WorkerSelectionStrategy** (#115 ✅ #116 ✅): `quarkus-workitems-api` pure-Java shared SPI module, `WorkItemAssignmentService` + `LeastLoadedStrategy` + `ClaimFirstStrategy` wired into create/release/delegate
- **CaseHub alignment issue** casehubio/engine#123 filed — steps for CaseHub to adopt `quarkus-workitems-api` SPI

## Immediate Next Step

**Architectural brainstorm: `quarkus-work` foundational module.**

The insight: `quarkus-workitems-api` is already a shared contract layer. We should design a
`quarkus-work-core` (or `quarkus-work`) module — a shared *implementation* layer containing
`WorkItemAssignmentService`, strategies, `WorkerRegistry`, and eventually the semantic embedding
SPI — usable by both WorkItems (human inbox) and CaseHub's TaskBroker (automated routing).

This reframes WorkItems from "human task inbox" to "foundational work management layer". If enough
migrates down, the project splits cleanly into `quarkus-work` (generic work substrate) and
`quarkus-workitems` (human inbox on top). We design our side regardless; CaseHub can align later.
Merge the module back if it turns out too thin.

Start with brainstorming this separation — evaluate all existing modules for what belongs at the
foundational `quarkus-work` level vs. what is WorkItems-specific. After the design is settled,
continue Epic #100 AI-native features (semantic matching, resolution suggestion, escalation summarisation).

## Priority Roadmap

*Unchanged — `git show HEAD~1:HANDOFF.md`*

## Open Issues

| Status | Issues |
|---|---|
| Active (#100) | Remaining: semantic matching, resolution suggestion, escalation summarisation |
| Active (#101–#106) | Business-hours deadlines, workload routing (now served by #115/#116), notifications, SLA reporting, subprocess spawning, multi-instance |
| Deferred | #117 RoundRobinStrategy (stateful cursor needed) |
| Blocked | #79, #39 |

## References

| What | Path |
|---|---|
| Design tracker | `docs/DESIGN.md` |
| WorkerSelectionStrategy spec | `docs/superpowers/specs/2026-04-20-worker-selection-strategy-design.md` |
| Confidence-gating spec | `docs/superpowers/specs/2026-04-20-confidence-gated-routing-design.md` |
| Epic priority table | `CLAUDE.md` Work Tracking section |
| CaseHub alignment | casehubio/engine#123 |
