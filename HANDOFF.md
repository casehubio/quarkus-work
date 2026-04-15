# Quarkus WorkItems — Session Handover
**Date:** 2026-04-15

## Project Status

All phases complete. `quarkus-workitems-examples` module added and fully passing. 0 open actionable issues.

| Module | Tests |
|---|---|
| runtime | passing |
| workitems-flow | 32 |
| quarkus-workitems-ledger | 74 (↑10 this session) |
| quarkus-workitems-examples | 4 |
| testing | 16 |
| integration-tests | 19 (native) |

## What Changed This Session

**New: `quarkus-workitems-examples`** — four runnable scenario endpoints, each covering a distinct set of ledger/audit capabilities. Run via `POST /examples/{name}/run`. All 4 `@QuarkusTest` scenarios pass.

**Two upstream prerequisites added:**
- `WorkItemService.complete(+rationale, +planRef)` and `reject(+rationale)` overloads — pass through the lifecycle event for ledger capture
- `LedgerEventCapture.deriveActorType()` — `agent:` prefix → AGENT, `system:` prefix → SYSTEM

**README ledger section** expanded from 1 bullet to 10 sub-items covering every capability.

**`deployment/pom.xml` fix** — removed spurious `quarkus-ledger-deployment` dependency that violated the Quarkiverse extension-descriptor rule.

**5 garden entries** submitted: 3 gotchas (extension-descriptor constraint, RestAssured Float/Double, quarkus-junit5 naming), 1 technique (@Transactional propagation), 1 undocumented (quarkus-ledger sibling install).

**`mandatory-rules.md` updated** in cc-praxis — new Content Focus rule: omit process/tooling narration from blog entries unless explicitly requested.

## Immediate Next Step

No immediate next step — project is in a clean state. Candidates:
- `workitems-flow/` needs a README (started end of session — see below)
- Issue #39 — ProvenanceLink PROV-O graph (blocked: CaseHub and Qhorus not ready)
- Quarkiverse submission (mdproctor → quarkiverse org)

## Open Issues

- #39 — ProvenanceLink PROV-O graph (blocked: CaseHub and Qhorus not ready)

## References

| What | Path |
|---|---|
| Examples README | `quarkus-workitems-examples/README.md` |
| Design spec | `docs/specs/2026-04-14-tarkus-design.md` |
| Ledger design | `docs/specs/ledger-design.md` |
| Blog (this session) | `blog/2026-04-15-mdp02-examples-that-prove-it.md` |
