# Technical Research: Revised Mayonnaise Scope

**Checked**: 2026-11-20
**Authority**: [spec.md](spec.md), [constitution v4.0.0](../../.specify/memory/constitution.md).
**Status: Phase 0 complete.** The requester confirmed there is no existing Spotify app
and approved VIP-only connected playlist submission. Normal and VIP participants import
catalog albums; Normal participants retain receiving/reviewing playlists. This supersedes
the earlier Normal link-only playlist requirement and closes P0-SOURCE.

## Evidence and decision boundaries

The original core-stack probe and the added SMTP probe are documented in
[compatibility.md](compatibility.md). The latter passes exact-version installation,
strict declaration checking, seven tests including five synthetic SMTP scenarios,
Next production compilation using its bundled Webpack, and a dated zero-finding audit.
No live Spotify app, Gmail account, deployed connection, real delivery, RLS/Realtime
application or physical-device acceptance has been tested. Those are implementation/
operator checks, not unknown source architecture.

Research decisions below govern the revised Phase 1 model/contracts/quickstart.
No application, SQL migration, package manifest or cloud resource is created by this plan.

## R-01 — Versions and actual compatibility

**Decision:** Node 24.x; Next 16.4.6; React/React DOM 19.3.1; TypeScript 5.9.3 strict;
Tailwind/PostCSS plugin 4.3.5; PostCSS 8.5.33; Lucide React 1.76.0; Supabase JS 2.119.2
and SSR 0.12.7; Zod 4.6.7; Vitest 5.1.1/test-only Vite 8.3.8; Playwright Test 1.65.1.

**Rationale:** The temporary fixture passed installation/peer resolution, strict type
checking with skipLibCheck:false, production build, two SDK/runtime-validation unit tests, and one
browser test using two independent contexts. TypeScript 6.0.3 failed on Supabase's WebAuthn
declarations. TypeScript 5.9.3 needed two missing URLPattern platform type names used by
Next; a narrow standard-conforming bridge resolved those without suppressing diagnostics.

TypeScript 7 is currently available and supported by Next's CLI path; do not claim Next
requires TypeScript 6. The chosen version is the combination actually tested here.
Use an exact lockfile and re-run compatibility checks before upgrading or removing the bridge.

**Alternatives considered:** Latest-everything without a build check, ignoring build
errors, globally disabling declaration checks, library patching, and an older unverified
Supabase SDK were not selected. The small platform bridge has an explicit retirement check.

**Sources:** [Next installation](https://nextjs.org/docs/app/getting-started/installation),
[Next bundled Webpack](https://nextjs.org/docs/app/api-reference/cli/next),
[Next TypeScript](https://nextjs.org/docs/app/api-reference/config/typescript),
[Supabase SSR release](https://github.com/supabase/ssr/releases/tag/v0.12.7),
[TS 5.9 DOM](https://raw.githubusercontent.com/microsoft/TypeScript/v5.9.3/src/lib/dom.generated.d.ts),
[TS 6 DOM](https://raw.githubusercontent.com/microsoft/TypeScript/v6.0.3/src/lib/dom.generated.d.ts),
[Tailwind Next setup](https://tailwindcss.com/docs/installation/framework-guides/nextjs),
[Lucide React](https://lucide.dev/guide/react),
[Vercel Node support](https://vercel.com/docs/functions/runtimes/node-js/node-js-versions).

## R-02 — Modular monolith and rendering boundaries

**Decision:** One root Next App Router application. Server Components read through
server-only DAL functions; Client Components own forms, browser state and Realtime.
Server Actions perform business form mutations; Route Handlers cover external auth/
Spotify callbacks, explicit sign-out, and integration operations where HTTP is necessary.

**Rationale:** Native full-stack boundaries avoid another backend and unnecessary HTTP
calls from Server Components to the application's own Route Handlers. Every entry point
authorizes independently; a layout or Proxy redirect is not an access-control system.

Disable Cache Components for this private MVP. Do not cache private DTOs, RSC/HTML/API
responses, cookies, or OAuth callbacks through ISR, use-cache, CDN or the service worker.
Preserve all Supabase SSR cookie/cache headers through Proxy response reconstruction.

**Alternatives considered:** Separate backend, client-only rendering everywhere, business
data through a service-role DAL, and shared persistent private caches add avoidable risk.

**Sources:** [Server/Client Components](https://nextjs.org/docs/app/getting-started/server-and-client-components),
[data security](https://nextjs.org/docs/app/guides/data-security),
[backend-for-frontend](https://nextjs.org/docs/app/guides/backend-for-frontend),
[Supabase SSR](https://supabase.com/docs/guides/auth/server-side/nextjs).

## R-03 — Free Auth email and administrator invitation delivery

**Decision:** Keep Supabase Auth email/password with confirmation enabled. Use an eligible
dedicated free Gmail SMTP sender for native Auth mail. Send application activation
invitations separately from server-only Next.js Node code through direct SMTP; Nodemailer
10.6.1 with @types/nodemailer 8.0.2 is the tested library/type pair. No queue, independent backend, purchased domain or paid mail
provider is needed for the proposed pilot architecture.

Use smtp.gmail.com:465 with implicit TLS, or :587 with required STARTTLS. Store the
application credential in protected server environment settings and the separate Auth
credential in Supabase SMTP settings. Use separate eligible App Passwords where available,
matching sender/SMTP identity, and no browser/CI secret exposure.

Default Supabase mail is team-recipient-only and two messages/hour, so it cannot serve
ordinary participants. Custom Auth SMTP starts at 30/hour; direct application SMTP does
not inherit that throttle. Google's personal-account SMTP allowance is up to 500
messages per rolling 24 hours across account clients. Configure a lower combined
operational allowance, conservative Auth project-wide limits and separate administrator
invitation limits. The same sender shares capacity between Auth and application mail;
30/hour sustained is not a safe daily budget. Exposed Auth signup/reset endpoints can
consume Auth quota even when RLS prevents private application access. Quota exhaustion
must fail understandably without paid recovery.

Initial technical settings for a dedicated sender: cap Auth email at 10 messages/hour
project-wide and application invitations at five issuance operations per administrator
per hour plus 20 project-wide per rolling 24 hours. This bounds planned Auth/application
traffic at about 260 per 24 hours before hourly-window boundary bursts and other sender
use, leaving headroom below the documented 500-message limit. Do not claim that an
hourly Auth setting alone enforces an exact combined rolling daily budget.
Count issuance/send attempts conservatively, including unknown
outcomes. These are operational throttles, not membership limits; verify the provider's
effective settings and the sender's other activity before release.

Keep native email confirmation separate from the application invitation. Do not replace
the latter with admin.inviteUserByEmail(), auto-confirm email or create administrator
authority from an Auth link. Native Auth email-token expiry remains separately configured
(typically one hour); the application code remains valid for 24 hours from its own
issuance. Google 2-Step Verification/App Password eligibility and account policy are
operator prerequisites, not setup already performed.

**Delivery decision:** Generate a cryptographically random 256-bit code in the trusted
SQL issuance routine and return it once to the issuing Node request. The ordinary action
returns only a safe receipt to the UI. Code exists only in DB/Node request memory;
never accept a caller-selected weak code/digest or persist a recoverable replay result.
Commit its digest, target,
operation identity, original issuance/expiry and send-attempt ownership before SMTP.
Await one bounded send attempt without an open database transaction; persist safe status
only. Distinguish attempting, SMTP-accepted, definitively failed and unknown. Require
acceptance of the sole intended envelope recipient; SMTP acceptance is not inbox arrival.

Repeated/concurrent requests with the same operation ID return the existing invitation
receipt/status without re-sending or recovering the raw code. Timeout, crash or lost
acknowledgement may leave acceptance uncertain; stale attempting becomes unknown and
must not trigger automatic replay. An administrator may explicitly issue a new invitation
with a new operation ID. That is distinct from retrying the original operation; do not
extend its expiry or silently revoke previous invitations. This is at-most-one application
attempt per operation, not a promise of exactly-once email delivery.

Use a fragment-based activation URL to avoid placing the raw code in normal server
request URLs. Disable SMTP debug/body logging and redact codes, full activation links,
credentials and message bodies. Opening/previewing never redeems. Explicit authenticated
activation checks the current trusted verified recipient email and consumes/activates
atomically.

**Compatibility evidence:** Node 24.20.0/npm 11.19.0, Nodemailer 10.6.1 (MIT-0,
Node >=20, no runtime dependencies) and @types/nodemailer 8.0.2 (MIT, existing Node types
deduplicated) pass strict TypeScript with skipLibCheck:false and npm ls. The separate
temporary full-stack fixture passes seven tests: two core checks plus actual-loopback
acceptance, recipient rejection, acknowledgement timeout after full receipt, verify
without sending and AbortSignal cancellation after full receipt. A supported
`next build --webpack` production build passes; Turbopack hit a worker-binding EPERM
in this environment. Use Next's bundled Webpack for the reproducible production command,
without adding another bundler dependency. A fresh npm audit reports zero findings for
the tested lockfile, not perpetual safety.

The runtime supports per-message AbortSignal while the current types omit it. Isolate
the verified `SMTPTransport.MailOptions & { signal: AbortSignal }` intersection at the
mail boundary; no any or diagnostic suppression. Use a 15-second total AbortController
deadline plus connection/greeting/socket timeouts. Promise.race alone does not cancel
the send; cancellation after possible receipt remains unknown. The runtime's nonpooled
transport.close() alone must not be assumed to cancel an active message.

The synthetic fixture uses unencrypted loopback only. It does not prove Gmail TLS,
App Passwords, outside-team arrival or deployed connectivity. See compatibility.md
for paths, hashes, commands and the earlier failed attempts. No real mail was sent.

**Rationale:** Vercel documents external SMTP from Functions, including Nodemailer.
A hash-only invitation cannot support a durable raw-code outbox/retry without a different
security decision. An honest unknown state is necessary when external acceptance cannot
be atomically committed with the database receipt.

**Alternatives considered:** Using native Auth invitations as application activation,
manual-only delivery, recoverable-code storage/outbox, unbounded retries, disabling email
confirmation, adding participants to the infrastructure team and a new paid sender service
were not selected. App Password availability and actual outside-team delivery remain
release checks; there is no demonstrated fundamental SMTP architecture blocker.

**Sources:** [Vercel SMTP guidance](https://vercel.com/kb/guide/sending-emails-from-an-application-on-vercel),
[Google SMTP limits](https://developers.google.com/workspace/gmail/imap/imap-smtp),
[App Password requirements](https://support.google.com/accounts/answer/185833),
[Supabase SMTP](https://supabase.com/docs/guides/auth/auth-smtp),
[Auth rate limits](https://supabase.com/docs/guides/auth/rate-limits),
[Auth invitations](https://supabase.com/docs/guides/auth/users),
[Nodemailer SMTP](https://nodemailer.com/smtp),
[message cancellation caveats](https://nodemailer.com/message),
[official releases](https://github.com/nodemailer/nodemailer/releases),
[type package](https://www.npmjs.com/package/@types/nodemailer?activeTab=versions).

## R-04 — Native sessions and trusted verification

**Decision:** @supabase/ssr request-scoped server clients and browser client use publishable
keys and user JWTs. Next Proxy refreshes supported cookies and preserves response headers.
Use getClaims/getUser as appropriate; never authorize from getSession's decoded server
cookie data. Activation reads the current confirmed email from trusted auth.users state.

**Rationale:** Supabase access JWTs may remain cryptographically valid after sign-out.
Every private read, RPC and Realtime SELECT policy also checks that its session_id still
exists in auth.sessions for the current user, that JWT time claims are valid, and that
application activation is current. Only own setup/activation/sign-out is allowed before
activation. No custom opaque session registry or paid session-management feature is needed.

Write RPCs acquire a shared Auth-session row lock before mutations; native session
deletion conflicts with that lock. Verify this serialization against local/hosted Auth.
Do not modify managed Auth table structure. Browser Auth/Realtime needs SDK-readable user
tokens; do not falsely label those cookies HttpOnly. Privileged keys/tokens stay server-only.

**Alternatives considered:** JWT claims alone cannot promptly revoke a copied token.
Unverified Spotify email, user-editable metadata, or a cached layout cannot prove authority.

**Sources:** [Sessions](https://supabase.com/docs/guides/auth/sessions),
[SSR client/caching guidance](https://supabase.com/docs/guides/auth/server-side/creating-a-client?queryGroups=framework&framework=nextjs),
[sign-out](https://supabase.com/docs/guides/auth/signout).

## R-05 — RLS with transaction-only mutations

**Decision:** Safe public application reads use RLS. Authenticated clients get no raw
table DML. Public invoker RPC wrappers call narrow private mutation functions owned by
one NOLOGIN/NOBYPASSRLS role that is not table owner; its RLS policies use the unchanged
JWT-backed auth.uid(). A separate tightly scoped private helper can inspect managed
Auth rows and return only current-actor authorization facts.

**Rationale:** A plain SECURITY INVOKER mutation needs DML grants and would allow REST
writes to bypass expectedVersion, atomicity and idempotency. A table-owner definer can
bypass RLS. The limited executor preserves both invariants and RLS. Internal sync fanout
has a constrained role policy and is reachable only through computed-audience routines.

Normal application traffic never uses service-role business reads/writes. A server-only
privileged client is justified solely for encrypted Spotify credential access, provider-
attested staging, necessary lifecycle maintenance, and trusted administrator/eligibility setup. It cannot
become a generic CRUD interface. User-JWT publication validates staged actor ownership and the applicable source/authority/grant
generation and writes first-party entities under RLS.

**Alternatives considered:** Broad authenticated table DML and broad service-role queries
would weaken the requested authorization model.

**Sources:** [RLS](https://supabase.com/docs/guides/database/postgres/row-level-security),
[roles](https://supabase.com/docs/guides/database/postgres/roles),
[functions](https://supabase.com/docs/guides/database/functions),
[PostgreSQL function privileges](https://www.postgresql.org/docs/current/sql-createfunction.html).

## R-06 — Supabase Realtime without private payload leakage

**Decision:** Publish UPDATE events only for participant_sync(participant_id, revision).
SELECT RLS requires the own user ID, live Auth session and activated account. Mutations
increment only the authorized audience; removal also invalidates the removed actor once.
Client subscriptions trigger current authorized refetch, not direct review merging.

**Rationale:** Postgres Changes authorizes each event; private Broadcast authorization is
cached until reconnect/new JWT. Tiny own-revision events avoid transmitting private
feedback or DELETE payloads. A two-second foreground direct Supabase liveness query
handles missed events, logout and lost permission even without a revision change.
Keep one overdue tick while refresh is in flight. This fallback complements Realtime;
it does not replace the selected service.

A direct feedback write invalidates only the sender and that reviewer. A group
feedback write invalidates current members so results and rankings can be refetched.
Removal invalidates the former member once, while later group activity must not
increment their revision. Retained former-member votes remain inputs for current
members; membership removal is not vote deletion. Read-result authorization is always
current, including through a browser back/forward cache, background/resume, stale RSC
responses and copied access tokens.

**Alternatives considered:** Broadcasting group comments/scores and publishing every
private table require unnecessary disclosure/revocation analysis. Vercel Functions do
not host a new WebSocket server.

**Sources:** [Realtime authorization](https://supabase.com/docs/guides/realtime/authorization),
[Postgres Changes](https://supabase.com/docs/guides/realtime/postgres-changes).

## R-07 — Roles, collections and review concurrency

**Decision:** Prepare the revised model around collections and reviews, preserving the
existing private access, invitation, group and occurrence structure. The recommendations
below are Phase 0 decisions to carry into Phase 1 for the approved source resolution; they are
not a replacement field-level contract.

Trusted account authority records administrator/common and Normal/VIP eligibility.
Administrator authority does not imply VIP eligibility, playlist ownership, group
creator rights or private-content access. New common accounts default to Normal under
disclosed A-01. Do not trust signup payloads or user-editable identity metadata.

Generalize playlist-only physical names to collections, collection recipients,
collection tracks and source-specific collection metadata/staging. Each share remains
a context-specific UUID with playlist/album kind, direct/group context, full/first-100
scope, captured/rateable counts and frozen occurrence positions. Distinct shares of
the same Spotify resource never share feedback automatically. Non-linked playlist submission is excluded from this MVP by the approved resolution.

Keep eligible occurrence identities separate from purgeable provider fields. Store
durable exclusion positions and bounded reasons without raw excluded payloads. At
publication, captured source positions must be covered exactly once by an eligible
occurrence or an exclusion. The first-100 boundary applies before exclusions; do not
reach into later positions to fill 100 eligible tracks. Duplicate provider track IDs
retain distinct occurrence UUIDs.

A review is created by the first authorized track score, manual general score or choice,
not only by the first track rating. Keep a nullable integer general half-point value
0–10, nullable favorite/least-liked occurrence references, and a general-feedback
optimistic version separate from individual track versions. Composite foreign keys
bind choices and ratings to the same collection; when both choices exist, they must
differ under A-03. An unset choice uses ordinary nullable FK semantics. The sender
remains ineligible to review their own share.

Track-first feedback inserts/finds the unique parent review with general version zero.
General-score/choice writes update their shared general-feedback version atomically;
unrelated track edits do not invalidate it. Concurrent first writes resolve to the
same unique parent. Authorize before returning conflicts, latest values or operation
receipts. No-op requests need not create empty reviews. Initial absence is required;
clearing an already recorded score is not approved merely because a column is nullable.
Do not silently add vote-deletion behavior to a patch contract.

Activate and consume a hashed 256-bit invitation code in one transaction using current
trusted verified email, the 24-hour issuance deadline, wall-clock time after lock waits,
and persistent denied-attempt counters. Retain the initial technical rate of five
attempts per authenticated account per 15 minutes; no denial consumes a valid invitation.

Activation first locks the current actor's trusted auth.users row to serialize verified
email changes. Then lock in order: genuine current Auth session; private account authority;
personal provider connection/generation when applicable; group; invitation/membership;
review/import and affected feedback; audience revisions sorted by participant UUID.
Authority changes, provider disconnect and group removal take compatible exclusive
locks. Recheck after external HTTP work without holding DB transactions across it.
Group writes use shared group/member locks; admission/removal uses an exclusive group
lock. Previously accepted invitations cannot readmit a removed member.

Source lifecycle distinguishes connected-personal origin/generation from independently
obtained application-catalog data. Personal disconnect must not purge independent
catalog imports or relabel connected data to evade erasure. Original source-total
evidence remains purgeable and distinct from the current provider list. Restoration
must reconcile current administrator/VIP assignments as well as native sessions,
invitation replay state and provider revocations.

**Rationale:** The approved additions need a few current-feedback fields and constraints,
not another service, an event-sourcing subsystem or new score tables. Restricted RPC
mutation paths enforce cross-row reviewer eligibility, which a row CHECK cannot safely
query from another table.

**Alternatives considered:** First-track-only review creation, a single version for
unrelated feedback, provider-ID deduplication, incomplete exclusion history, participant-
editable roles and blanket metadata retention/disconnect rules are incompatible.

**Sources:** [PostgreSQL constraints](https://www.postgresql.org/docs/current/ddl-constraints.html),
[locks](https://www.postgresql.org/docs/current/explicit-locking.html),
[transaction isolation](https://www.postgresql.org/docs/current/transaction-iso.html),
[time functions](https://www.postgresql.org/docs/current/functions-datetime.html).

## R-08 — Official Spotify paths and source-specific lifecycle

**Decision:** Preserve the one-application connected pilot within actual provider
eligibility. For VIP owner/collaborator playlists use S256 PKCE, exact loopback/production
HTTPS callbacks, encrypted personal grants, full pagination and before/after playlist
snapshot comparison. Read the provider account ID with the minimal supported profile
scope; never use Spotify email as Mayonnaise verified-email proof.

For albums select the official application-only Client Credentials flow as the selected
no-personal-connection route. Server-held application credentials obtain a catalog
token; individual album and album-tracks endpoints provide metadata and paginated
occurrences. An explicit configured ISO market is required. Process pages up to the
documented 50-item page maximum, preserving returned order, disc/track numbers and
duplicates. Apply the approved capped alternative scope without confusing a source
page maximum with a 100-item product boundary. Do not add participant OAuth or a
playlist-owner requirement for a catalog release.

Album identity, title, artists, cover URLs, release information and total_tracks are
documented fields, with missing/unavailable states. Album responses have no documented
snapshot_id. Freeze internal occurrence identity/order and record capture time; do not
invent a provider guarantee of atomic album snapshot consistency. Project-app access,
market behavior, multi-disc ordering and scope coverage still require authenticated
implementation tests. This is a documented architecture, not a successful live test.

Freeze application occurrence structure rather than permanent Spotify payloads.
Retain the existing 24-hour technical freshness/revalidation-or-hide policy and source
attribution. Connected sources carry the applicable personal connection/generation;
application-catalog imports carry their own actual origin. Revalidation must not match
a changed playlist by position/title and attach the wrong metadata to a frozen occurrence.
Hide unidentifiable stale metadata.

Applicable personal-data purges occur within the existing five-day policy, normally
immediately. Tokens, provider IDs, URLs, artwork references and controlled copies require
the lifecycle audit; they are not exempt merely because first-party reviews remain.
Late OAuth, refresh and import responses must not restore revoked eligibility. Broader
retained first-party feedback remains conditional under QC-005, not declared anonymous
by replacing a title with a placeholder.

**Rationale:** The official album route does not need a participant's personal grant.
That does not establish public-playlist access: R-12 records the approved exclusion
of Normal playlist submission. The developer owner's existing Premium is a prerequisite,
not permission to extract arbitrary lists.

**Alternatives considered:** Personal-account reuse for Normal imports, web-player token
acquisition, HTML extraction, extra client IDs, treating album IDs as playlist snapshots
and indefinite archived provider metadata are not selected.

**Sources:** [Client Credentials](https://developer.spotify.com/documentation/web-api/tutorials/client-credentials-flow),
[Get Album](https://developer.spotify.com/documentation/web-api/reference/get-an-album),
[Get Album Tracks](https://developer.spotify.com/documentation/web-api/reference/get-an-albums-tracks),
[quota modes](https://developer.spotify.com/documentation/web-api/concepts/quota-modes),
[playlist items](https://developer.spotify.com/documentation/web-api/reference/get-playlists-items),
[profile](https://developer.spotify.com/documentation/web-api/reference/get-current-users-profile),
[refresh](https://developer.spotify.com/documentation/web-api/tutorials/refreshing-tokens),
[terms](https://developer.spotify.com/terms), [policy](https://developer.spotify.com/policy).

## R-09 — PWA, Tailwind and Lucide

**Decision:** Native app/manifest.ts and a small authored TypeScript service worker
compiled into public assets. Cache only explicit public assets/offline shell. Tailwind
styles semantic controls; Lucide uses named imports, decorative aria-hidden icons or
accessible labels where the icon conveys an action.

**Rationale:** No PWA plugin, push/VAPID stack, player, or private offline store is needed.
React escaping and boundary validation protect comments; no untrusted HTML rendering.
Actual mobile installation, accessibility and resume checks remain required.

**Alternatives considered:** Serwist/next-pwa, a UI framework, offline mutation queues and
full icon imports add dependencies beyond current needs.

**Sources:** [Next PWA](https://nextjs.org/docs/app/guides/progressive-web-apps),
[Tailwind Next guide](https://tailwindcss.com/docs/installation/framework-guides/nextjs),
[Lucide React](https://lucide.dev/guide/react).

## R-10 — Free limits and account conditions

All values are checked on 2026-11-20 and must be rechecked before deployment.

| Service | Included free resources | Pilot controls/estimate |
| --- | --- | --- |
| Vercel Hobby | 100 GB Fast Data Transfer, 10 GB Fast Origin Transfer, 1M CDN requests and 1M function invocations; 4 active CPU-hours and 360 GB-hours provisioned memory/month; current maximum function duration 300 seconds; runtime logs one hour. | <200k function invocations/month; short bounded imports. Stay Hobby, no trial/add-on/usage purchases. Accept suspension rather than paid overages. Personal/noncommercial use only. |
| Supabase Free | 500 MB DB/project; 50k Auth MAUs; 5 GB uncached plus 5 GB cached egress; two active free projects; one-day API/DB logs. | <250 MB DB, <2 GB/month egress. No automatic backups or uptime SLA. Stay Free; quota errors/read-only/pausing are explained failures. |
| Supabase Realtime Free | 200 peak connections, 2M messages/month, 256 KB maximum message. | <=10 expected connections and <200k messages/month; own-revision payload is tiny. Measure fanout; never send private payloads to reduce design effort. |
| Gmail SMTP / Supabase Auth | Free eligible sender; up to 500 SMTP messages/rolling 24 hours shared across account clients. Auth custom SMTP initially 30/hour; direct application SMTP has its own limits. | Five-participant fixture; conservative Auth project-wide and application invitation throttles plus shared daily headroom. Confirm outside-team delivery. No purchased domain, paid Workspace or participant Google accounts. |
| GitHub Free / private repo | Current included compute is $12/month, about 2,000 standard Linux two-core minutes at $0.006/minute if exclusively used there; 500 MB shared artifacts/Packages and 10 GB cache/repo. | Budget <300 Linux minutes/month; short artifact retention and bounded caches. Other eligible workloads can share the compute pool. Zero paid usage must be enforced. |

The stored 40,000-rating dataset is not a monthly write assumption. For 10,000 confirmed
mutations/month and ten listening clients, allow up to 100,000 delivered revision events,
then margin to the 200,000-message target. About 100,000 RSC refetches, 10,000 actions and
20,000 initial/navigation reads fit the 200,000-function target. If all 40,000 ratings
were saved in a month with ten-client fanout, notifications alone could reach 400,000;
re-evaluate the measured envelope rather than confuse tiny payloads with fewer messages.
These are sizing assumptions, not product limits.

Vercel Hobby cannot import private organization-owned GitHub repositories. Verify personal
repository ownership and linked commit-author identity; do not silently publish/transfer
the repository or upgrade. Opt out of Vercel AI training in Team Data Preferences before
repository import/deployment because the setting is prospective.

GitHub budgets must stop usage, not merely alert. Configure effective zero paid Actions/
storage/cache usage before the first workflow; no-valid-payment-method accounts are a
documented hard-stop fallback. Included recurring compute is a free entitlement, not
trial credit. If the account cannot enforce zero charges, use local validation and
leave hosted CI/deployment blocked until configuration is corrected.

Supabase may pause after seven low-activity days; current restoration window is one year.
Encrypted controlled first-party exports and tested restore remain necessary. Use free
platform subdomains, not paid custom domains. The existing owner Premium confirmation
is D-006; no new subscription is authorized.

**Sources:** [Vercel Hobby](https://vercel.com/docs/plans/hobby),
[pricing](https://vercel.com/pricing), [terms](https://vercel.com/legal/terms),
[data opt-out](https://vercel.com/changelog/updates-to-terms-of-service-march-2026),
[Git eligibility](https://vercel.com/docs/git),
[Supabase pricing](https://supabase.com/pricing),
[Realtime message counting](https://supabase.com/docs/guides/platform/manage-your-usage/realtime-messages),
[cost control](https://supabase.com/docs/guides/platform/cost-control),
[pausing](https://supabase.com/docs/guides/platform/free-project-pausing),
[backups](https://supabase.com/docs/guides/platform/backups),
[GitHub Actions billing](https://docs.github.com/en/billing/concepts/product-billing/github-actions),
[budgets](https://docs.github.com/en/billing/how-tos/set-up-budgets).

## R-11 — Tests, CI and dependency discipline

**Decision:** Vitest for domain/server logic and synchronous units; local Supabase/pgTAP
and concurrent integration checks for RLS/races; Playwright against running Next for
async Server Components, native Auth and two-context Realtime behavior. Add three-session
and outsider privacy cases required by the revised spec. Actual device validation is separate.

GitHub CI uses standard Linux runners, npm ci, pinned action revisions, minimal permissions,
concurrency cancellation, explicit job timeouts, disposable local SQL fixtures and no live
credentials for PR tests. Do not use pull_request_target to execute untrusted PR code.
Use manual release of the tested main commit until automatic deployment can enforce gates;
CI is required, automatic production deployment is not.

**Rationale:** Next's official Vitest guidance does not support async Server Components.
The nine-story scope requires explicit checks for trusted roles/tier rejection,
administrator mail failure/uncertainty, album pagination and capped coverage, same-
collection choices, independent track/general edits, exact group means, retained votes,
zero/no-score distinction, tie ordering, three visible ranking slots and group revocation.
Normal playlist denial must also be exercised through direct server/RPC attempts;
a synthetic album fixture cannot claim wider provider access.
The temporary probe demonstrates dependency integration only; local fixture cookies do not
prove real Supabase authentication or Realtime. Those tests are mandatory implementation work.

**Development-tool pin:** Supabase CLI 3.2.0 (MIT) starts through npm under Node 24;
version/help smoke checks are recorded in compatibility.md. Its documented PostgreSQL 17
support preserves the selected DB major. No local stack or SQL application test ran.
Use its current local-by-default reset command; do not retain removed --local flags.

**Additional dependency justification:** @supabase/ssr is the supported cookie adapter;
PostCSS integrates Tailwind; Vite is Vitest-only; Zod protects untrusted boundaries;
server-only enforces privileged import boundaries; Supabase CLI/pgTAP verify SQL;
axe-core may provide automated accessibility evidence. No Firebase, Cloudflare, ORM,
alternate backend, or additional runtime service is retained. Gmail is solely the
necessary SMTP transport. Dependencies require pinned versions, licenses and vulnerability
review before delivery.

**Sources:** [Next Vitest](https://nextjs.org/docs/app/guides/testing/vitest),
[Next Playwright](https://nextjs.org/docs/app/guides/testing/playwright),
[database tests](https://supabase.com/docs/guides/database/testing),
[GitHub secure workflows](https://docs.github.com/en/actions/security-for-github-actions/security-guides/security-hardening-for-github-actions).

## R-12 — Approved source resolution

**Decision:** Close P0-SOURCE through the requester's explicit scope revision, not an
import bypass. No Spotify Developer Dashboard app exists. Only VIP participants may
submit playlists through their own authorized owner/collaborator connection. Both
Normal and VIP use application-authenticated album catalog import; Normal may receive
and review playlist shares. The first-100 boundary remains for catalog albums, while
connected playlists consider their full declared scope.

**Rationale:** Official preview/embed interfaces do not establish ordered playlist export.
User Guidelines prohibit scraping; Widget Terms constrain Widget-content reuse.
Earlier visible public samples proved neither permitted extraction nor complete
first-100 coverage. New-app playlist-items access requires the connected owner/
collaborator. The requester's approval removes the infeasible Normal playlist path
from the current acceptance criteria; it does not claim provider rules changed.

The February announcement distinguishes older apps, but that avenue does not apply
because the requester confirmed no app exists. A new app must satisfy current eligibility,
the developer-owner Premium prerequisite and five-account connected pilot. Broader
playlist submission remains DP-06, outside this MVP.

**Alternatives considered:** Unapproved HTML scraping, anonymous Web Player tokens,
borrowing a VIP grant, extra client IDs, paid extraction and silently adding a file/
manual track editor are rejected. Keeping Normal playlist submission would leave a
source gap; the requester instead approved the narrowed MVP.

**Sources:** [oEmbed](https://developer.spotify.com/documentation/embeds/reference/oembed),
[User Guidelines](https://www.spotify.com/us/legal/user-guidelines/),
[Widget Terms](https://developer.spotify.com/documentation/embeds/terms),
[playlist items](https://developer.spotify.com/documentation/web-api/reference/get-playlists-items),
[2026 migration guide](https://developer.spotify.com/documentation/web-api/tutorials/february-2026-migration-guide),
[developer announcement](https://developer.spotify.com/blog/2026-02-06-update-on-developer-access-and-platform-security).

## R-13 — Group means, ranking and feedback visibility

**Decision:** Derive aggregates from confirmed current feedback; add no score/ranking
tables or ranking service. Use integer half-point inputs and exact numeric arithmetic.
Track mean is sum(track half-points) / (2 × rated occurrence count); with no scores it
is absent, not zero. Manual general score remains independent and does not change progress.

For each group-context collection, group score is sum(non-null general half-points) /
(2 × contributing review count). Include zero, exclude missing, give each contribution
equal weight, and display contributing count. Current membership authorizes the viewer;
do not filter retained review authors to current members, because disclosed A-06 preserves
their votes. Direct submissions have no co-recipient aggregate.

Rank only that group's context-specific shares with at least one general contribution,
partitioned by playlist/album. Compare means before display rounding, then immutable
collection UUID ascending for A-05 ties. Use sequential positions (ROW_NUMBER) rather
than competition ranks, preserving exactly three highlighted positions. Return three
nullable highlighted slots and rows four onward; store no fake placeholder collections.
A partial track review may contribute a recorded manual score under A-04. Derive means
and positions from one authorized query snapshot, and round only the display to nearest
hundredth with upward ties.

Favorite/least-liked choices and comments never alter the group grade. The same eligible
occurrence set, reviewer identity and direct/group audience govern them. A shared visual
score primitive must express the same numeric value through stars and accessible text,
without silently rounding the group result to a manual half-step.

**Rationale:** Derived queries avoid duplicate aggregate state and simplify concurrent
edits. Current group authorization must be explicit; a generic collection-read predicate
could disclose direct-recipient feedback. Invoker views/RPC queries preserve RLS; do
not introduce a table-owner aggregate path.

**Alternatives considered:** Averaging track means, weighting by rated-track count/VIP,
discarding former-member votes on removal, merging identical Spotify resources across
contexts, public/personal rankings, cached rounded grades and extra podium positions
are incompatible with the confirmed rules/defaults.

**Sources:** [PostgreSQL window functions](https://www.postgresql.org/docs/current/tutorial-window.html),
[security-invoker views](https://www.postgresql.org/docs/current/sql-createview.html),
[numeric types](https://www.postgresql.org/docs/current/datatype-numeric.html).

## R-14 — Post-analysis identity, bootstrap and SMTP decisions

**Decision date:** 2026-11-20. These decisions implement the owner's review corrections;
they are not evidence that an account, migration or application has been deployed.

**Initial administrator:** provision the first administrative profile by a protected
operator operation against an exact, already verified Auth UUID, without a Mayonnaise
invitation. This resolves the circular dependency that otherwise requires an existing
administrator to invite the first administrator. Constitution v4.0.0 approves only that
exception. Use native Auth signup/admin tooling for identity creation, never hand-written
inserts into managed Auth tables. Do not treat createUser's email_confirm option as proof
of mailbox ownership. Record the bootstrap in a unique durable operation receipt; a retry
for the same identity is a no-op, and a different identity cannot bootstrap later.
Subsequent authority maintenance operates only on already activated members. It uses
separate operator credentials, not a runtime service-role bootstrap endpoint.

**Alternatives:** an administrator-invitation setup screen adds an unnecessary flow and
does not solve first-issuer trust; first-signup promotion allows account takeover during
initial deployment; a raw auth.users insert couples the app to managed Auth internals.
A versioned SQL procedure plus operator script is sufficient and requires no new service.

**Identity policy (owner correction):** usernames are unique ASCII alphanumeric handles,
displayed as @username. Accept bare input or one leading @, reject every remaining
character outside A–Z/a–z/0–9, then lowercase with a fixed ASCII mapping. Reject spaces,
underscores, punctuation, accents and all other Unicode/special characters without
trimming, NFKC conversion or transliteration. Store the bare lowercase value/key and
compare bytewise. Shared UI presentation supplies exactly one @ prefix; DTOs keep the
bare value. The owner rejected the earlier free-form printable-Unicode proposal, so
Unicode collation/case-folding support is unnecessary. Keep the existing canonical-key
column equal to username rather than introducing a separate display-name feature.
The same policy serves signup/setup, operator bootstrap and literal member search;
empty/lone-@ search returns no results. Verify the spec vectors, strict whole-string
validation, case/prefix collisions and presentation consistency in app/SQL/E2E tests.
No X API, external handle lookup, new dependency or exact copy of X's account rules is
introduced. UUIDs remain the authorization identity.

Email has a separate ASCII-mailbox policy: boundary-whitespace trimming and ASCII
lowercasing, no Unicode/IDNA rewrite or provider-specific dot/plus folding. Supabase Auth's
current validateEmail implementation validates format and lowercases the result; the
application explicitly defines its narrower single-mailbox input policy rather than
assuming every Auth-accepted input has suitable SMTP/identity semantics. Revalidate the
trusted verified email during redemption and fail closed on unsupported input. ASCII
Punycode domains are allowed as supplied; SMTPUTF8 is not promised. This scope decision
avoids silently binding an invitation to a rewritten address. Native verification is
still required; normalization alone does not establish ownership or inbox delivery.

**SMTP:** separate lifecycle status from a finite safe reason. The model exhaustively
enumerates 11 codes and compatible pairs. This is application classification, inferred
from transport stage, final response and submission evidence; it is not a one-to-one
translation of Nodemailer's error.code. Require final DATA acceptance, not just RCPT
acceptance. Definitive rejection/pre-submission failure is failed; possible acceptance
without acknowledgement is unknown. A crash or lost DB finalization is unconfirmed,
including when a stale attempting row remains physically unchanged. Neither failed nor
unknown authorizes automatic resend. Preserve invitation consumption/expiry and prior
account activation on failed authentication. SQL/loopback/application tests are planned
in T012–T013 and T022–T032; earlier probe success does not cover all new mappings.

**Sources checked:** [Supabase admin identity creation](https://supabase.com/docs/reference/javascript/auth-admin-createuser),
[Supabase Auth email validation source](https://github.com/supabase/auth/blob/master/internal/api/mail.go),
[PostgreSQL 17 collations](https://www.postgresql.org/docs/17/collation.html),
[Nodemailer error fields](https://nodemailer.com/errors),
[Nodemailer SMTP transport](https://nodemailer.com/smtp),
[SMTP final reply semantics](https://datatracker.ietf.org/doc/html/rfc5321#section-4.2.5).
Source branches and hosted versions can change; recheck these contracts at implementation.

## Closure

All required architecture/source/dependency decisions are resolved for the approved
scope. P0-SOURCE is closed by explicit product approval, and the SMTP exact-version
compatibility check is complete with its stated limits. The owner-approved constitution
v4.0.0 amendment now permits restricted initial administrator bootstrap without an
application invitation. Revised model/contracts/quickstart and the existing 136 tasks
include the post-analysis corrections; review all ten principles against this version.

Real account configuration, source access tests, outside-team mail delivery, full SQL/
RLS/session/concurrency/Realtime acceptance, free-quota measurement and device checks
remain mandatory implementation/release work. Do not report them as executed.
