# Quarkus Tarkus — Session Handover
**Date:** 2026-04-14

## What This Project Is

Quarkus Tarkus is a new standalone Quarkiverse extension providing **human-scale WorkItem
lifecycle management** — a human task inbox with expiry, delegation, escalation, priority,
and audit trail. It emerged from design work on quarkus-qhorus Phase 10 (human-in-the-loop
controls), where it became clear the human task inbox is a cross-cutting concern that
should live in its own extension rather than any one project.

## Why "WorkItem" Not "Task"

Three systems in the ecosystem all use "task" to mean different things:
- CNCF Serverless Workflow SDK (`io.serverlessworkflow.api.types.Task`) — machine-executed workflow step
- CaseHub (`io.casehub.worker.Task`) — CMMN case work unit
- Tarkus (`io.quarkiverse.tarkus.runtime.model.WorkItem`) — human-resolved unit of work

Using `WorkItem` avoids naming collisions and accurately describes what Tarkus manages:
work that waits for a person.

**Rule:** A `Task` is controlled by a machine. A `WorkItem` waits for a human.

## What Was Done

Project scaffolded as a Quarkiverse extension (parent + runtime + deployment):
- `CLAUDE.md` — full project context for future sessions
- `docs/specs/2026-04-14-tarkus-design.md` — primary design specification
- `docs/DESIGN.md` — implementation-tracking design document
- `runtime/pom.xml` — Panache, Flyway, REST, Scheduler, H2, PostgreSQL
- `deployment/pom.xml` — all transitive deployment JARs (learned from quarkus-qhorus)
- `deployment/.../TarkusProcessor.java` — feature registration stub
- `runtime/src/main/resources/application.properties` — default config
- No Java source yet — scaffold only

## Integration Modules (Future, Not Yet Scaffolded)

| Module | What it does |
|---|---|
| `quarkus-tarkus-flow` | Implements `TaskExecutorFactory` SPI — Quarkus-Flow `humanTask` steps route into Tarkus |
| `quarkus-tarkus-casehub` | CaseHub `WorkerRegistry` adapter — human-capability tasks become WorkItems |
| `quarkus-tarkus-qhorus` | Qhorus MCP tools — `request_approval`, `check_approval`, `wait_for_approval` |

## Immediate Next Step — Phase 1: Core Data Model

Following the same TDD approach as quarkus-qhorus. Start with:

1. **`TarkusConfig.java`** — `@ConfigMapping(prefix = "quarkus.tarkus")` with Javadoc on ALL methods (required by quarkus-extension-processor)
2. **`WorkItemStatus.java`** — enum: PENDING | ASSIGNED | IN_PROGRESS | COMPLETED | REJECTED | DELEGATED | ESCALATED | EXPIRED
3. **`WorkItemPriority.java`** — enum: LOW | NORMAL | HIGH | CRITICAL
4. **`WorkItem.java`** — PanacheEntityBase, UUID PK, all fields per design spec
5. **`AuditEntry.java`** — PanacheEntityBase, UUID PK, append-only audit log
6. **`WorkItemService.java`** — create, assign, claim, complete, reject, delegate
7. **`V1__initial_schema.sql`** — Flyway migration: `work_item` + `audit_entry` tables
8. **Test resources** — H2 in-memory datasource (`application.properties` in `src/test/resources/`)
9. **Smoke test** — `WorkItemSmokeTest.java` — Quarkus boots, migration runs, WorkItem CRUD works

## Key Gotchas (Learned from quarkus-qhorus)

1. **`@ConfigMapping` requires Javadoc on ALL methods** including group accessors — missing one gives a cryptic compile error
2. **Deployment POM must list ALL transitive deployment JARs** — the `extension-descriptor` goal validates this; run `mvn install -DskipTests` first if you hit issues
3. **Install deployment first** — `mvn install -DskipTests` before running runtime tests so the extension-descriptor can find the deployment artifact in local Maven repo
4. **`key` is a reserved word in H2** — use `work_item_key` or similar if you need a key column

## References

| What | Path |
|---|---|
| Design spec | `docs/specs/2026-04-14-tarkus-design.md` |
| Implementation tracker | `docs/DESIGN.md` |
| Origin project (Qhorus) | `~/claude/quarkus-qhorus` |
| Quarkus-Flow (integration target) | `~/dev/quarkus-flow` — `TaskExecutorFactory` SPI in `io.serverlessworkflow.impl.executors` |
| CaseHub (integration target) | `~/claude/casehub` — `WorkerRegistry` in `casehub-core` |
| Claudony (UI surface target) | `~/claude/claudony` |
| Plan file | `~/.claude/plans/mellow-puzzling-seal.md` |
