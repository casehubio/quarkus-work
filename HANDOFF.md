# Quarkus WorkItems — Session Handover
**Date:** 2026-04-22

## Project Status

697+ tests, 0 failures. Two major features shipped this session.

| Module | Tests |
|---|---|
| quarkus-work-api | 23 |
| quarkus-work-core | 38 |
| runtime | 548 |
| quarkus-workitems-ai | 48 |
| (others unchanged) | — |

## What Was Built This Session

**`quarkus-work` / `quarkus-workitems` separation (issue #118 ✅)**

Extracted two new modules (groupId `io.quarkiverse.work`):
- `quarkus-work-api` — pure Java SPI: `WorkerCandidate`, `SelectionContext` (now with `title` + `description`), `AssignmentDecision`, `AssignmentTrigger`, `WorkerSelectionStrategy`, `WorkerRegistry`, `WorkEventType`, `WorkLifecycleEvent`, `WorkloadProvider`, `EscalationPolicy`, `SkillProfile`, `SkillProfileProvider`, `SkillMatcher`
- `quarkus-work-core` — Jandex library: `WorkBroker`, routing strategies, filter engine

Deleted: `quarkus-workitems-api`, `quarkus-workitems-filter-registry`

**Semantic skill matching — Epic #100 (#121 ✅)**

In `quarkus-workitems-ai/skill/`:
- `WorkerSkillProfile` entity + Flyway V14 + REST at `/worker-skill-profiles`
- `SemanticWorkerSelectionStrategy` — `@Alternative @Priority(1)`, auto-activates on classpath
- `EmbeddingSkillMatcher` — cosine similarity via `dev.langchain4j:langchain4j-core`, `Instance<EmbeddingModel>` optional injection (returns -1.0 gracefully when no provider)
- `WorkerProfileSkillProfileProvider` — default, reads `WorkerSkillProfile` entity
- `CapabilitiesSkillProfileProvider` — `@Alternative`, joins capability tags
- `ResolutionHistorySkillProfileProvider` — `@Alternative`, aggregates past completed items

Key gotchas captured in garden: `quarkus-langchain4j-core` stalls `@QuarkusTest` (use plain `dev.langchain4j` + `Instance<T>` instead); `*IT` tests silently skipped by Surefire; CDI ambiguity when multiple `@ApplicationScoped` beans implement the same SPI.

Also: sent CaseHub an alignment document covering `quarkus-work-api` + `quarkus-work-core` adoption steps.

## Immediate Next Step

Continue **Epic #100** — AI-suggested resolution:
`GET /workitems/{id}/resolution-suggestion` — LLM call with similar past completed WorkItems as context. Design: call `WorkItemStore` for N most similar completed items (same category, or embedding similarity), pass as few-shot examples to a `ChatLanguageModel`, return suggested resolution JSON. Lives in `quarkus-workitems-ai`.

## Priority Roadmap

*Unchanged — `git show HEAD~1:HANDOFF.md`*

## Open Issues

| Status | Detail |
|---|---|
| #119 open | CompositeSkillProfileProvider — merge profiles from multiple sources (deferred) |
| #120 open | SemanticWorkerSelectionStrategy fallback to LeastLoaded on embedding failure (deferred) |
| #117 deferred | RoundRobinStrategy (stateful cursor) |
| #79, #39 blocked | External integrations and provenance |
| Ledger drift | `quarkus-workitems-ledger` API drift with `quarkus-ledger` sibling — separate session needed |

## References

| What | Path |
|---|---|
| Design tracker | `docs/DESIGN.md` |
| Separation spec | `docs/superpowers/specs/2026-04-22-quarkus-work-separation-design.md` |
| Semantic matching spec | `docs/superpowers/specs/2026-04-22-semantic-skill-matching-design.md` |
| work-api SPIs | `quarkus-work-api/src/main/java/io/quarkiverse/work/api/` |
| work-core strategy | `quarkus-work-core/src/main/java/io/quarkiverse/work/core/strategy/` |
| work-core filter | `quarkus-work-core/src/main/java/io/quarkiverse/work/core/filter/` |
| AI skill/ package | `quarkus-workitems-ai/src/main/java/io/quarkiverse/workitems/ai/skill/` |
| Epic priority table | `CLAUDE.md` Work Tracking section |
