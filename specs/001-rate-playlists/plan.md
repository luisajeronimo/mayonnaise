# Implementation Plan: Mayonnaise — Private Playlist and Album Reviews

**Branch**: `main` | **Date**: 2026-11-20 | **Spec**: [spec.md](spec.md)
**Feature identifier**: `001-rate-playlists`, returned by the setup script for the
existing feature directory. No branch was created/switched.
**Status**: Phase 0 research and Phase 1 design complete for the approved revised scope.
This is implementation planning, not a claim that the application is built or deployed.

## Summary

Build one modular full-stack Next.js App Router monolith: strict TypeScript, Tailwind,
Lucide, a responsive dark-only PWA, and managed Supabase PostgreSQL/Auth/Realtime.
Host within Vercel Hobby and use GitHub Free for versioning/CI, with zero paid usage.

Activated administrators send private single-use account invitations. Common accounts
are Normal or VIP under trusted authority. **Only VIP may connect a personal Spotify
account and submit its owned/collaborative playlists. Normal and VIP import catalog
albums without a personal connection, capped at the first 100 source positions.**
Normal participants retain receiving/reviewing playlists. The requester explicitly approved
this scope after confirming no older Spotify app exists; P0-SOURCE is closed by that
decision, not by scraping or an undocumented bypass.

Collections are context-specific direct/group shares with frozen occurrence identities.
Track scores/means, independent manual general score and optional favorite/least-liked
choices are separate. Current group members read individual reviews, an equal-weight
manual-score mean and separate playlist/album rankings, including three highlighted
positions and remaining rows. Direct reviews remain private to each sender/reviewer pair.

Usernames are unique lowercase ASCII alphanumeric handles, displayed consistently as
@username. Signup/setup, bootstrap and search accept a bare handle or one leading @;
reject spaces, underscores, accents and symbols. Email has its own separate policy.

Nine prioritized stories, 88 FRs and 45 measurable outcomes govern implementation.
Disclosed A-01–A-09 defaults remain visible in the specification; broader playlist import,
deletion/role-management screens and unrelated prototype features are not added.

## Technical Context

**Language/Version**: Node 24.x (probe24.20.0), npm 11.19.0, TypeScript 5.9.3,
`strict:true`, `skipLibCheck:false`. Keep the narrow verified URLPattern type bridge
and independent strict WebWorker compiler project. No ignored diagnostics/any workaround.

**Primary Dependencies**: Next 16.4.6; React/React DOM19.3.1; Tailwind and PostCSS plugin4.3.5;
PostCSS 8.5.33; Lucide React 1.76.0; Supabase JS2.119.2 and SSR0.12.7; Zod4.6.7;
server-only 0.0.1. SMTP uses Nodemailer 10.6.1 with @types/nodemailer 8.0.2 and an isolated
verified AbortSignal type intersection. Vitest 5.1.1/test-only Vite8.3.8, Playwright 1.65.1
and development-only Supabase CLI 3.2.0. Exact lockfile/licensing purposes and observed
compatibility are in [research.md](research.md) and [compatibility.md](compatibility.md).
Use the Next bundled `--webpack` production build command validated by the added probe;
no separate bundler dependency or backend is introduced.

**Storage**: Supabase PostgreSQL 17, UUIDs/FKs/composite keys/checks/indexes, versioned SQL
migrations and RLS. 23 canonical entities/185 fields in [data-model.md](data-model.md).
First-party immutable structure is separate from purgeable provider data/secrets/staging.
No ORM, stored score/rank tables, private offline database or extra storage service.

**Testing**: Vitest domain/runtime boundaries; local Supabase/pgTAP and concurrent SQL/
native-session tests; Playwright against running Next with at least two independent
contexts plus third-party/outsider privacy cases. Actual iOS/Android install, VoiceOver
and TalkBack are separately recorded. Seven added-probe tests, strict compilation,
production build and a zero-finding dated dependency audit passed their limited scope;
full application/live source/mail/RLS/Realtime acceptance is not executed.

**Target Platform**: HTTPS PWA for Safari/iOS and Chrome/Android, responsive from 320 CSS px,
200% text, targets >=44 by 44 CSS px, safe areas/orientation/keyboard support. Dark mode
only, inspected Figma tokens/fonts/icons and supplied brand assets. Verify actual browser
versions against Tailwind's documented baseline.

**Project Type**: One root App Router application. Server Components/request-scoped DAL
read using user JWT/RLS; small Client Components handle interaction/subscriptions.
Business mutations use Server Actions; protocol/auth/Spotify handlers use Route Handlers
where needed. No independent backend or WebSocket server.

**Performance Goals**: Confirmed authorized changes in track/general/choice feedback,
group means/ranks and membership converge in foreground views within five seconds under
documented network/service/quota conditions. Eligible share/import target60seconds after
required authorization. Bounded continuation/source/mail requests, no unbounded retries.

**Constraints**: Constitution v4.0.0, permanent free resources, zero incremental spending
and no paid overages/trials. Existing developer-owner Premium is the sole subscription
exception. New Spotify app/current eligibility and five-account connected pilot apply.
No borrowed personal grant, HTML scraper, paid extractor, extra client ID, playback,
push or silent file/manual fallback. Vercel personal/noncommercial eligibility,
repository compatibility, prospective content-training opt-out and effective GitHub
zero-paid-usage configuration are required before deployment/CI.

**Scale/Scope**: Sizing fixture, not membership cap: five activated participants, at most
ten simultaneous sessions,100 shares averaging100 eligible occurrences and up to four
reviewers/share:10,000 occurrences,40,000 stored track ratings, up to400 general-feedback
rows. Stored capacity is not monthly write volume. Assume10,000 mutations and 20,000 initial/
navigation reads/month; worst ten-session fanout permits100,000 revision deliveries/
refetches before headroom. Targets <250MB DB, <2GB DB egress/month, <200,000 Realtime
messages and Vercel function invocations/month, <300 standard Linux CI minutes/month.
Two-second liveness uses direct Supabase RPC, not Vercel. Measure album pages, result
DTO size, metadata, mail, source quotas and shared CI. Growing Normal membership requires
new consumption estimates, never a fictional two-person/five-member business limit.

## Constitution Check

Pre-research identified source feasibility, SMTP compatibility and revised authorization/
arithmetic as required evidence. The explicit import-scope approval and completed probes
resolve those architecture questions. The table is design compliance, not delivery-test
completion. The owner-approved constitution v4.0.0 amendment permits only restricted
initial administrator provisioning without an application invitation. All later accounts
retain invitation activation; the existing Premium exception is unchanged.

| Principle | Pre-research gate | Post-design decision and planned evidence |
| --- | --- | --- |
| I. Simplicity First | One app required; extra services need justification. | PASS: one Next monolith, managed Supabase, small SMTP helper; derived score/rank queries. No extractor/queue/ORM/independent backend. |
| II. Zero-Cost Infrastructure | Source/delivery paths and free caps required validation. | PASS: documented Hobby/Free entitlements, shared free SMTP, enforced zero-paid CI, existing Premium only. Quota/account configuration and measured load gate release. |
| III. Mobile First | PWA/mobile critical flows required. | PASS: responsive dark system, authored public-only worker, install/update/browser-lifecycle and actual-device checks. |
| IV. Security by Design | Roles and catalog path changed authorization. | PASS: trusted role/tier, genuine current sessions, limited executor/RLS, pair-private direct feedback, group current membership, source-specific attestation and one-use verified-email activation for subsequent accounts, plus auditable operator-only initial administrator bootstrap without an application invitation. |
| V. Real-Time Consistency | New scores/rank changes and removal need convergence. | PASS: own-revision invalidation, direct liveness RPC, current authorized refetch, independent optimistic versions, transactional computed audience and lifecycle generations. |
| VI. Type Safety | New SMTP pair needed proof. | PASS: exact strict/declaration-checked probe, runtime unknown decoding, DB types, narrow verified platform/signal bridges; separate strict worker check. |
| VII. Testable Development | Revised critical outcomes needed mapping. | PASS: nine-story/45-outcome runbook covers source modes, email uncertainty, roles, track/manual/choice, aggregate/rank/privacy/races. No fixture claims live provider success. |
| VIII. Database as Code | Expanded entities require revised SQL history. | PASS:23 entities/185 fields, constraints/grants/RLS/functions/jobs/publication versioned; clean rebuild/forward upgrade and replay-safe restore tests. |
| IX. Accessibility | New controls/podium must remain perceivable. | PASS: shared semantic components, same numeric/star value, visible empty slots, contrast/focus/44px/200% text and keyboard/VoiceOver/TalkBack checks. |
| X. Maintainability | New dependencies/state must be justified. | PASS: internal domain boundaries, one visual system, current setup/contracts, source-specific lifecycle and reproducible tested versions/license/audit records. |

**Post-design gate: PASS against constitution v4.0.0, including the approved narrow
initial-administrator exception. No additional exception is authorized.**
Before delivery, require actual SMTP/source/account configuration, hosted plan eligibility,
paid-usage prevention, complete critical tests, migration/restore checks and real mobile/
latency/accessibility evidence. Failure blocks release; no automatic paid recovery.

## Project Structure

### Documentation (this feature)

```text
specs/001-rate-playlists/
├── spec.md
├── plan.md
├── research.md
├── compatibility.md
├── import-feasibility.md
├── change-impact.md
├── design-reference.md
├── design/
│   ├── tokens.css
│   └── figma-inspection.json
├── data-model.md
├── quickstart.md
├── contracts/
│   ├── http-api.md
│   ├── database-rpc.md
│   ├── spotify.md
│   └── ui-sync.md
├── tasks.md
└── checklists/requirements.md
```

Model/contracts/quickstart are now current for the approved scope. Visual evidence and
brand masters are retained. [tasks.md](tasks.md) now contains 136 dependency-ordered tasks
for all nine stories and all 185 canonical fields. Run `$speckit-analyze` before
implementation; task generation executed no application work.

### Source Code (repository root)

Proposed implementation layout; only supplied public brand masters already exist.

```text
src/
├── app/
│   ├── (public)/
│   ├── (private)/
│   ├── auth/
│   ├── api/auth/sign-out/
│   ├── api/spotify/
│   ├── manifest.ts
│   └── globals.css
├── components/
│   ├── ui/
│   └── realtime-boundary.tsx
├── features/
│   ├── access/
│   ├── invitations/
│   ├── discovery/
│   ├── groups/
│   ├── collections/
│   └── reviews/
├── lib/
│   ├── supabase/
│   ├── authorization/
│   ├── spotify/
│   ├── mail/
│   ├── validation/
│   └── database.types.ts
├── service-worker/
│   ├── index.ts
│   └── tsconfig.json
└── proxy.ts
types/next-urlpattern.d.ts
public/
├── brand/logo.svg
├── favicon.svg
├── icons/
└── offline.html
supabase/
├── config.toml
├── migrations/
├── tests/
└── seed.sql
tests/
├── unit/
├── integration/
├── contract/
└── e2e/
scripts/
├── bootstrap-administrator.ts
├── maintain-account-authority.ts
├── build-service-worker.ts
└── backup.ts
.github/workflows/ci.yml
next.config.ts
postcss.config.mjs
vitest.config.mts
playwright.config.ts
tsconfig.json
package.json
package-lock.json
.env.example
```

**Structure Decision**: Root Next project with small feature modules and shared visual
primitives/private shell. Group results/rankings stay with groups/reviews rather than
a separate service. Server-only provider/mail adapters are internal boundaries with a
specific current purpose. Use one dark token/typography/icon system and Next font
facilities with licensed self-hosted assets. Preserve supplied masters; no unrelated
Downloads files are design sources. Prototype dimensions guide responsive proportions.

## Phase 0 Research Resolution

[research.md](research.md) records decision/rationale/alternatives, dated official sources,
free-resource limits and estimates. [import-feasibility.md](import-feasibility.md) records
the initially failed public-playlist approach and explicit approved resolution.
[compatibility.md](compatibility.md) separates actual core/SMTP/CLI evidence from untested
live application and operator checks. No required architecture question remains open
for the approved scope.

## Phase 1 Design

[data-model.md](data-model.md) defines 23 entities/185 fields and source-specific lifecycles.
[Web/actions](contracts/http-api.md), [SQL RPC](contracts/database-rpc.md),
[Spotify](contracts/spotify.md) and [UI/Realtime/PWA](contracts/ui-sync.md) define permissions,
wire types, state transitions, retries/concurrency and verified integration boundaries.
[quickstart.md](quickstart.md) is the configuration/validation runbook, not implementation
code or tests already run. [design-reference.md](design-reference.md) governs shared dark
visuals and faithful treatment of missing screens.

Private writes lock current native session/account authority, relevant connection,
group and feedback/import, then sorted revision rows. Final publication uses user JWT/RLS
and trusted staging. The privileged adapter is narrowly limited to provider data,
lifecycle and an already-claimed SMTP outcome finalizer. Initial bootstrap/authority
maintenance uses a separate protected operator connection, not runtime integration RPCs.
No service-key
business DAL, administrator content override or direct co-recipient aggregate.

Own-revision events invalidate; get_sync_state returns exact revision text directly from
Supabase every 2 seconds in foreground. Retain an overdue tick, revalidate on lifecycle
changes and ignore late generations. Server-confirmed native logout precedes SDK
credential cleanup; offline logout conceals and blocks account switching until revocation.

### Verification mapping

| Story / requirements | Planned evidence / success criteria |
| --- | --- |
| US1: FR-001–004, FR-035–043, FR-064–066, FR-087 | Native sessions, initial-only admin bootstrap, setup normalization, failed-login state preservation, role/tier spoofing, hash-only single-use/expiry/race/guessing and enumerated SMTP outcomes/uncertainty/retry; SC-001, SC-013–017, SC-045 and the access/setup subset of SC-033. Browser discovery and actual playlist-submission denial are US2 evidence. |
| US2: FR-005–012, FR-030–033, FR-036, FR-044–062, FR-067–070, FR-085, FR-088 | VIP playlist/catalog album sources, scope/metadata/exclusion-code validation, bare/leading-@ discovery/follows and direct/group grants; SC-002–003, SC-010, SC-012, SC-018–031, SC-034–037 and the discovery/playlist-eligibility subset of SC-033, including denial to a Normal administrator. |
| US3: FR-012–018, FR-022, FR-024, FR-033–034, FR-063, FR-071–075, FR-086 | Exact track/manual arithmetic, manual-first/choice-only feedback, same-collection choices and independent versions; SC-004, SC-007, SC-032, SC-038–039. |
| US4: FR-002, FR-004, FR-021–024, FR-048, FR-061–062, FR-086 | Two-context convergence, current authorization/removal, missed events/offline/resume/race; SC-005, SC-009, SC-028, SC-043–044. |
| US5: FR-019–020, FR-029, FR-057, FR-063 | Track comment/input isolation and score-independent edits; SC-007, SC-023, SC-032. |
| US6: FR-009, FR-011, FR-025–026, FR-030, FR-048, FR-054, FR-056, FR-062 | Context-specific history/frozen scope/retention/current permission; SC-006, SC-010, SC-027–029. |
| US7: FR-027–029, QC-001, QC-006 | Install/update/mobile/keyboard/cache/visual consistency and assistive technology; SC-008, SC-011. |
| US8: FR-072, FR-076–078, FR-086 | Group-only manual mean/count, zero/missing/retained votes and direct isolation; SC-040, SC-044. |
| US9: FR-026, FR-079–085, FR-086 | Per-group/type ranks, exact ties,0..4 entries, three slots, numeric/star equality and live reorder; SC-041–044. |

### Delivery order and current task list

Start with reproducible root tooling/design primitives and SQL/native-session boundaries;
then account activation/administrator mail, discovery/direct/group authorization, source-
specific import/publication, track/general feedback/choices, revision/lifecycle handling,
group results/rankings and mobile/history acceptance. Critical SQL/race/privacy tests
accompany the relevant behavior. [tasks.md](tasks.md) expands these boundaries into
136 tasks, per-story checkpoints, explicit dependencies and field/requirement coverage.

## Complexity Tracking

No constitutional violations require an exception table. Added SMTP handles approved
administrator delivery; limited SQL executor/session locks prevent raw-DML/version and
revocation bypasses; source-specific metadata protects retention; derived aggregates
avoid duplicate state. Each has a current requirement and a documented simpler alternative.
