# quarkus-work — Session Handover
**Date:** 2026-04-27

## Project Status

631 runtime tests, 39 notifications tests, 19 calendar tests. Full build passes.
casehub-connectors: 15 core tests + 4 email tests.

## What Was Done This Session

### Epic #101 — Business-hours deadlines — CLOSED
- `BusinessCalendar` + `HolidayCalendar` SPIs in `quarkus-work-api`
- `DefaultBusinessCalendar`, `ConfigHolidayCalendar`, `ICalHolidayCalendar`
- `HolidayCalendarProducer` (`@Produces @DefaultBean`) — user-provided bean wins automatically
- V19 migration: `default_expiry_business_hours`, `default_claim_business_hours` on template
- `expiresAtBusinessHours` / `claimDeadlineBusinessHours` on `WorkItemCreateRequest`
- Precedence: absolute timestamp > business hours field > config default
- Example: `POST /examples/business-hours/run` + `GET /examples/business-hours/preview`

### Epic #103 — Notifications — CLOSED
- `quarkus-work-notifications` module: `NotificationChannel` SPI, `WorkItemNotificationRule` entity, `NotificationDispatcher` (`AFTER_SUCCESS` observer, async virtual threads), `NotificationRuleResource` CRUD
- `HttpWebhookChannel` (HMAC-SHA256), `SlackNotificationChannel`, `TeamsNotificationChannel`
- Now delegates HTTP logic to `casehub-connectors`

### casehub-connectors — NEW repo at casehubio/casehub-connectors
- Standalone reusable connector library: `Connector` SPI + `ConnectorMessage`
- `connectors-core`: `SlackConnector`, `TeamsConnector`, `TwilioSmsConnector`, `WhatsAppConnector`
- `connectors-email`: `EmailConnector` (quarkus-mailer, separate module)
- Local path: `~/claude/casehub-connectors`
- Fully wired into `casehub-parent`: `aggregator.xml`, `build-all.sh`, `full-stack-build.yml`, `dashboard.yml`, `pr-dashboard.yml`

### CLAUDE.md
- Linter added Platform Coherence Protocol + deep-dive URLs at top — intentional, keep
- `casehub-engine` local path corrected: `~/claude/casehub-engine` (not `~/dev/`)

## Open / Next

| Priority | What |
|---|---|
| 1 | #104 SLA compliance reporting — breach rates, percentile response times |
| 2 | #106 Multi-instance tasks — design check needed (may be CaseHub concern) |
| 3 | #93 Distributed SSE — Redis pub/sub for WorkItemEventBroadcaster |
| — | casehub-work-casehub adapter — belongs in casehub-engine; issue #136 tracks it |

## Key References

- Layering architecture: `docs/architecture/LAYERING.md`
- casehub-connectors local: `~/claude/casehub-connectors`
- casehub-connectors repo: `https://github.com/casehubio/casehub-connectors`
- `@DefaultBean` is `io.quarkus.arc.DefaultBean` — NOT `jakarta.enterprise.inject`
- Optional module Flyway migrations: use V3000+ namespace (not V1)
