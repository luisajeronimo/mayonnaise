# Quickstart and Validation Guide

**Date**: 2026-11-20 | **Status**: Current Phase 1 setup/acceptance contract.
**Authority**: [plan.md](plan.md), [data-model.md](data-model.md), [contracts/](contracts/),
[spec.md](spec.md).

## Current execution boundary

The repository contains specifications/design assets, not a runnable application.
Commands in the local setup/check sections become runnable after the implementation
creates the documented scripts, manifest, lockfile, SQL history and fixtures. Do not
treat the previous 140-task list as current implementation instructions. Follow the
regenerated [tasks.md](tasks.md) after consistency analysis; no task is completed by generation.

Actual temporary dependency results are in [compatibility.md](compatibility.md):
core stack plus Nodemailer strict/build/synthetic SMTP tests and a dated audit.
No cloud account, email send, Spotify app, real Auth/RLS/Realtime application or mobile
installation was configured/tested by this plan.

## Prerequisites and project configuration

Use Node 24.x/npm 11, the exact dependency matrix in compatibility.md and a free compatible
container runtime. Supabase CLI is a development-only local SQL/test tool; pin version 3.2.0 with
PostgreSQL 17 local config. Playwright uses the selected browser
test version. Preserve AGENTS.md, current docs, Git history and supplied public brand
masters when implementing the root App Router/src layout. No separate backend, ORM,
additional UI framework or PWA plugin is needed.

Create only free eligible resources when reaching implementation: Supabase Free,
Vercel Hobby personal/noncommercial and GitHub Free with effective zero-paid-usage
controls before any hosted workflow. No trial, paid domain, add-on or automatic upgrade.

Supabase configuration:

1. Use PostgreSQL 17 locally and verify hosted major/version. Keep application structure
   in versioned migrations, not dashboard-only SQL.
2. Enable email/password and confirmation. Native Auth email-token expiry is <=1 hour
   per current guidance; it is separate from the 24-hour application invitation.
3. Configure exact local/production site and redirect URLs, using127.0.0.1 locally and
   stable HTTPS production origin. No wildcard production callbacks.
4. Configure custom SMTP for ordinary outside-team Auth mail; default team-only delivery
   is insufficient. Initial Auth project email rate10/hour is separate from app limits.
5. Keep private schema outside Data API; apply limited executor/grants/RLS, safe RPCs and
   participant_sync UPDATE publication only. Publish no review/provider payload or DELETE.
6. Local disposable config enables email confirmation and captured mail, not real sends.
   Automated seed/reset helpers refuse non-loopback/live project origins.

### Provision the initial administrator before inviting participants

This is an operator procedure under constitution v4.0.0, not a new application screen.
After migrations are available and before any ordinary account activation:

1. Create/select the initial identity through official Supabase Auth signup/admin tooling.
   Complete native email verification using the controlled mailbox. This confirmation is
   not a Mayonnaise invitation. Do not raw-insert auth.users or auto-confirm an unverified
   address merely to bypass ownership checks; never store a real password in migrations.
2. Obtain the exact verified Auth UUID. Run the planned scripts/bootstrap-administrator.ts
   using the protected operator administration connection, with that UUID, a username
   satisfying the canonical identity policy and an explicit Normal/VIP tier (Normal default).
   The script is not implemented yet; docs/operations/accounts.md must provide its tested
   invocation and rollback/failure procedure when T032 is implemented.
3. Bootstrap atomically initializes the profile, active administrator authority, own sync
   row and unique nonexpiring installation receipt; it creates/sends/consumes no application
   invitation. Same-UUID retry has no new effects; a different UUID is refused. Prior
   activation or a bootstrap receipt closes fresh bootstrap, even after admin downgrade.
4. Sign in normally, then invite all subsequent accounts through the administrator flow.
   New common users default Normal. Later administrator promotion applies only to an
   already invitation-activated account using scripts/maintain-account-authority.ts;
   it never reopens invitation-free membership. An administrator with Normal tier still
   cannot connect personal Spotify or submit playlists.

Bootstrap and later authority maintenance are separate operator operations; no public
admin-claim endpoint, first-signup privilege or additional role-management screen exists.
Keep operator/restore credentials outside application runtime configuration. Preserve the
bootstrap receipt and activated_at history in backups/restores; if historical activation
exists without the receipt, stop for protected recovery rather than allowing fresh bootstrap.
No real administrator account or email has been created by this planning update.

Username and email examples/allowed inputs are defined in
[spec.md](spec.md#identity-input-and-comparison-policy); SQL policy and SMTP outcome pairs
are canonical in [data-model.md](data-model.md#identity-normalization-and-comparison).
Verify the ASCII handle and email canonicalization vectors locally and on hosted Auth/DB
before release. Username accepts only ASCII letters/digits, lowercased and displayed with
one @ prefix; optional input @ is not stored. Reject underscores, spaces, punctuation and
Unicode handle characters rather than rewriting them. Email keeps its separate validation
and boundary-whitespace policy; do not apply handle restrictions to email addresses.

## Free mail configuration and checks

Use an eligible dedicated free Gmail sender with two-step verification/App Password
support. Configure smtp.gmail.com:465 implicit TLS or587 required STARTTLS, matching
sender/SMTP identity. Supabase's App Password stays in protected Auth SMTP settings;
the application's separate eligible App Password stays in protected Node environment
settings. No participant Google login, Workspace plan or domain purchase is required.

App invitations use Nodemailer 10.6.1, single recipient, nonpooled verified TLS and a
15-second AbortSignal deadline. Use connection/greeting/socket timeouts inside that
budget, disable debug/body/file/URL loading, and await sending before returning.
A narrow tested signal type intersection is documented in research.md.

Set app issuance limits5/admin/hour and 20/project/rolling24h with serialized DB checks.
Auth's10/hour configuration plus app traffic leaves substantial headroom under the
documented personal Gmail allowance of 500/rolling24h, but account for boundary bursts
and other sender clients. SMTP accepted is not inbox arrival. Repeated same operation
never re-sends; unknown requires explicit review/new issuance, not raw-code recovery.

Before release, test sender eligibility/TLS/authentication, actual deployed connection,
outside-team application invitation, native confirmation and recovery arrival. Complete
verified matching-email activation. Exercise rejection, outage, timeout after possible
acceptance, quota exhaustion and admin-session loss during outcome finalization.
Do not manually confirm ordinary production users or silently purchase recovery service.

## Spotify setup

No existing Spotify app exists; create one new eligible Development Mode app only during
implementation. The developer owner's already-held Premium prerequisite is confirmed;
no new purchase is approved. Register exact loopback and stable HTTPS callback URLs and
allowlist the pilot's connected VIP accounts within current allowance.

VIP playlist submission requires the submitter's own owner/collaborator connection.
Normal endpoint/UI attempts are denied, while receiving/reviewing playlists remains
available. Normal and VIP album import uses server-side Client Credentials and an explicit
ISO market, without a personal connection. Catalog first 100 source positions are the
maximum before exclusions; disclose a cutoff. No Embed scraper or alternate personal
service account.

Review [Spotify contract](contracts/spotify.md) for pagination, snapshots, source-specific
attestation, refresh/disconnect and purge. Actual catalog/app access and connected
playlist eligibility are mandatory integration checks, not already verified credentials.

## Environment contract

| Setting | Exposure and purpose |
| --- | --- |
| NEXT_PUBLIC_SUPABASE_URL | Browser/server project URL. |
| NEXT_PUBLIC_SUPABASE_PUBLISHABLE_KEY | Publishable key; native session/RLS still mandatory. |
| SUPABASE_SERVICE_ROLE_KEY | Server-only narrow provider/staging/lifecycle/mail-finalizer adapter; never feed/review/group DAL or client. |
| SPOTIFY_CLIENT_ID, SPOTIFY_CLIENT_SECRET | Server application registration/catalog credentials; secret never browser-exposed. |
| SPOTIFY_REDIRECT_URI, SPOTIFY_MARKET | Exact callback and explicit album market. |
| TOKEN_ENCRYPTION_KEY, TOKEN_ENCRYPTION_KEY_ID | Server-only authenticated provider-envelope encryption/rotation. |
| APP_SMTP_HOST, APP_SMTP_PORT, APP_SMTP_USER, APP_SMTP_PASSWORD, APP_SMTP_FROM | Protected Node SMTP config; no credential defaults in production. |
| APP_ORIGIN, PRIVACY_CONTACT_EMAIL | Exact local/HTTPS app origin and working operator/privacy contact. |
| Trusted operator administration connection | Migration/initial bootstrap/activated-account authority maintenance/recovery only; absent from application runtime and public settings. |

Use ignored .env.local, fictitious .env.example values and protected deployment environment
settings. No production secrets in PR/fork previews, fixtures, screenshots, logs, build
artifacts or AI tool output. SSR cookies use the supported adapter; preserve cache/cookie
headers and never authorize from decoded getSession alone.

## Local setup after implementation

Run from the repository root with disposable local Supabase only:

```bash
npm ci
npx --no-install supabase start
npx --no-install supabase db reset
npm run fixtures:seed -- --local
npm run dev -- --hostname 127.0.0.1
```

CLI 3.2.0 resets locally by default and uses --remote for a remote target; the old --local
flag is not accepted. Local test wrappers verify loopback configuration and reject
--remote before any reset/seed.

Seed fictitious administrator (Normal), VIP, Normal reviewer, another member and outsider,
plus inactive/unverified identities. Codes/IDs are synthetic. Native Auth fixture setup
may verify local test accounts only; no live project reset, seed or personal source access.

The future package scripts must make these checks reproducible:

| Command | Expected purpose/result |
| --- | --- |
| npm run typecheck | Route type generation, strict application/declaration check and separate strict WebWorker check. |
| npm test | Vitest exact arithmetic, decoders, source/role guards, state/retry/conflict and mail-boundary checks. |
| npm run test:db | Local pgTAP schema/grants/RLS and migration-history checks. |
| npm run test:integration | Local native Auth/session, concurrent SQL/RPC, source attestation and SMTP fake-server contracts. |
| npm run build | Compile public-only service worker, then next build --webpack without ignoring errors. |
| npm run test:e2e | Playwright against running built Next/local Supabase; at least two independent browser contexts plus third/outsider cases. |
| npm run test:spotify | Explicit protected integration run with real eligible app/sample authorization, never a default PR test. |

Use Vitest for synchronous/domain logic and Playwright for async Server Components.
Install Playwright browsers through its pinned tooling. Generated database types must
match migrations; no any, ts-ignore or skipLibCheck workaround. Check the recorded
URLPattern and AbortSignal bridges before upgrades/removal. Full implementation audit
must include the final project lockfile/tooling, not reuse the temporary zero-findings result.

## Acceptance runbook

Record versions, dataset, service/network conditions and actual results. The following
procedures map all45 measurable outcomes; none is marked executed here.

| Criteria | Procedure and observable result |
| --- | --- |
| SC-001, SC-013–017, SC-033, SC-045 | Verify admin/common and Normal/VIP spoof rejection, inactive/anonymous/current-session denial, initial setup, separate confirmation/activation, wrong-current-email/code/expiry boundary, simultaneous single redemption, persistent guess limits and ordinary later login. At least three invitees activate; no two-person cap. Sending failure/unknown grants no activation, consumption, replay send or extended expiry. |
| SC-002–003, SC-010, SC-012, SC-030, SC-034–037 | VIP own/collaborator playlist import covers all pages/stable snapshot. Normal playlist attempts fail at server/RPC; Normal/VIP catalog album import requires no personal connection.60-item and 120-item fixtures prove complete vs first 100 scope, exclusions-before-total, duplicates, multi-disc order and honest cover/artist/missing-data states. No zero-rateable publication. |
| SC-004, SC-007, SC-032, SC-038–040 |0..5 half-step scores, null/zero distinction, track/general independence, favorite/least same-share checks, manual-first/choice-only review, persistence and visible optimistic conflicts. Track 2.5/3 yields 2.75 partial, adding 5 yields 3.5 complete;0/0/0/0.5 yields 0.13; duplicate4/2 yields 3. Manual5/2 gives group3.5, then manual 0 gives 2.33, excluding absent scores regardless of track counts. |
| SC-018–023, SC-031 | Search/follow identities only; two direct recipients each see own review only, sender sees both. Outsider/follower/forwarded link gains no grant. Unfollow removes new-recipient eligibility, preserves old direct grants/reviews/reverse follow/groups. Same source shared elsewhere never merges feedback. |
| SC-022–029, SC-044 | Creator/non-creator posting, creator-only invite/remove, target acceptance/full earlier history, no pending/removed/admin override. Old accepted invite cannot readmit. Race removal with posting/review/read, preserve group history/votes and independent direct grants, no later events to removed actor. |
| SC-026, SC-039, SC-043 | Concurrent duplicate acceptance/publication/feedback, same-key replay, conflicting-key reuse, general-vs-track version separation and stale same-resource conflict. No duplicate memberships/shares/votes, lost edit or stale rank regression. |
| SC-041–043 | For each type prepare0,1,2,3,4 rankable entries. Exactly three highlighted slots, later positions below, real zero rankable, absent mean not fake zero, deterministic exact-mean ties, same grade in stars/text. General edit reorders only its authorized group/type; retained former-member votes still count. |
| SC-005, SC-009, SC-028, SC-043–044 | Alternate ten persisted changes across at least two independent authenticated contexts and third session. Measure <=5 seconds to authorized visible state under recorded conditions. Outsider receives no private data. Test missed/duplicate/out-of-order events,2.5-second refresh cycle, membership loss, reconnect and no offline write/false success. |
| SC-006, SC-008, SC-011 | Reopen ten-share mixed history and verify current authorized state. Check installed iOS Safari/Android Chrome,320px/200% text,44px targets, contrast, keyboard/VoiceOver/TalkBack, safe areas and stable shared dark visual roles. |

Also exercise copied revoked JWT/native logout races, offline pending signout/account-switch
blocking, background/resume/bfcache, stale RSC callbacks and service-worker updates.
Inspect private-cache absence in browser/CDN/service worker. Race provider disconnect/
downgrade against OAuth, refresh, source stages and metadata; connected purge must not
delete independent catalog albums or restore revoked state. Test controlled data erasure
and replay-safe backup restoration.

## CI, free limits and release

Before the first hosted workflow, enforce GitHub Free zero-paid-usage/Stop usage across
compute, artifacts/storage/cache; alert-only budgets are insufficient. Use standard Linux,
pinned action SHAs, minimal read permissions, cancellation/timeouts, short-lived fictitious
artifacts, local SQL fixtures and no production secrets/untrusted pull_request_target.
Target <300 standard Linux minutes/month and measure the shared allowance.

Vercel Hobby requires personal/noncommercial eligibility, supported repository ownership
and linked deploy author; a private organization repo is not silently transferred/published.
Opt out of content training before import/deployment. Use Node 24, stable HTTPS callbacks,
bounded60s actions, protected secrets and no standalone WebSocket server. Release only a
reviewed/tested main commit until automatic deployment can enforce the same gates.

Use the dated free-limit table and estimates in research.md. Measure DB/index size, source
pages, result DTO egress, revision fanout/refetches, function CPU/memory/time, mail and CI
usage. Simulate pause, read-only/quota failures,429, lost connections and sender failure;
show unavailability rather than paid recovery or false confirmation.

Controlled encrypted first-party exports and tested restore are necessary because Free
has no automatic backup/SLA. Exclude provider data/credentials, sessions and replay-sensitive
invitation state; reconcile authority/revocations before reopening. Pauses may stop cleanup;
operator contact/resumption procedures must still meet applicable erasure obligations.

Delivery requires all critical automated checks, migration rebuild/upgrade, real source/
mail setup, two-context convergence, actual mobile/accessibility evidence and zero-cost
settings. A successful dependency probe alone satisfies none of those application gates.
