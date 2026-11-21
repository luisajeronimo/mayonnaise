# Web, Server Action and DTO Contracts

**Date**: 2026-11-20 | **Status**: Current Phase 1 design.
**Authority**: [spec.md](../spec.md), [data-model.md](../data-model.md).
All interfaces below are planned; no application endpoint exists yet.

## Entry boundaries

Use one App Router application. Server Components read through request-scoped server-only
DAL with user JWT/RLS. Client Components receive minimal authorized DTOs and own interactive
forms/subscriptions. Business mutations use Server Actions with runtime validation and
per-action authorization; a layout/Proxy redirect is insufficient. Private responses,
RSC/HTML, redirects, auth/integration callbacks and errors use private/no-store semantics.
Preserve Supabase SSR cookie/cache headers and avoid cross-user persistent Next/CDN caches.

Protocol Route Handlers:

| Route | Contract |
| --- | --- |
| GET /auth/confirm or /auth/callback | Complete the configured native Supabase email/PKCE flow; validate type/token/return path and preserve SSR cookies. Never redeem an application code. |
| GET /api/spotify/authorize | Live activated VIP, initiate one-use session-bound PKCE; strict local return path. |
| GET /api/spotify/callback | Validate state/session/VIP authority/generation, exchange code server-side, consume state once; no code/token logging. |
| POST /api/auth/sign-out | Same-origin current-session native revocation before acknowledging browser cleanup. No GET sign-out. |
| GET /manifest.webmanifest and /service-worker.js | Public authored install assets; no private data or credential caching. |

Spotify callbacks use exact 127.0.0.1 loopback or configured stable HTTPS origin.
Application invite codes use the activation-page URL fragment, removed from visible
browser history after memory capture, never query strings/server access logs. A preview
or GET has no redemption side effect. Browser-only draft state cannot persist the code
or private feedback across logout.

## Shared action envelope and wire rules

Every mutation accepts a UUID operationId plus a validated domain payload. IDs are UUIDs;
actor/session/issuer/reviewer come from current trusted authentication, never request
claims. Canonical normalized semantic-request digests detect conflicting reuse of one
operation ID; server-generated secrets, attempt IDs and timestamps are excluded.

Reply is a discriminated union: success with the authorized result DTO/receipt, or typed
failure with safe code, English message, optional retryAt and authorized current version/
value for a conflict. Codes include AUTH_REQUIRED, ACTIVATION_REQUIRED, SETUP_REQUIRED, FORBIDDEN,
VALIDATION_ERROR, VERSION_CONFLICT, OPERATION_CONFLICT, RATE_LIMITED, SOURCE_UNAVAILABLE,
PROVIDER_REAUTH_REQUIRED, IMPORT_INCOMPLETE, SMTP_FAILED, SMTP_UNKNOWN and
SERVICE_UNAVAILABLE. Do not expose row existence, tokens, private payloads, SQL details
or another user's conflict state. Authorize again before replaying a receipt.

Scores enter as integer half-points 0..10. Display converts to 0..5 without accepting
out-of-range/non-half-step values. Bigint versions/revisions are decimal strings on the
wire. Means use a decimal display string (at most two fractional digits), contributing
count, and optional exact numerator/denominator decimal strings when needed for contracts;
client display never becomes the ranking source. Absence is null, distinct from zero.
Timestamps are ISO UTC. Every external value is decoded from unknown at the boundary.

## Business actions

| Action | Payload beyond operationId | Success and essential checks |
| --- | --- | --- |
| completeProfileAction | username | Initial own setup, optional input @ and strictly ASCII alphanumeric handle/lowercase key per model; no role/tier/key fields accepted. |
| issueAccountInvitationAction | recipientEmail | Live active administrator; commit invitation/one send claim, await one bounded SMTP attempt, return invitation ID, immutable issued/expires times and sending status. Never return a recoverable code in a replay. |
| redeemAccountInvitationAction | rawCode | Genuine current verified matching email; atomic activation/consume; count denied attempts. Code only in request memory and explicit POST action. |
| followAction / unfollowAction | targetParticipantId | Active directional relation; no reciprocal admission, content grant or cascade to existing shares. |
| createGroupAction | name | Creator plus initial current membership atomically. |
| inviteGroupMemberAction | groupId, targetParticipantId | Creator only, active followed target, pending invite grants no access. |
| acceptGroupInvitationAction | invitationId | Target only; one membership; old accepted invite cannot readmit removed target. |
| removeGroupMemberAction | groupId, targetParticipantId | Creator only; revoke group access, preserve retained feedback and independent direct grants, cancel pending invites. No unapproved succession/leave flow. |
| startImportAction | collectionKind, sourceUrl, audience | Source URL decoded to fixed Spotify kind/ID; playlist requires VIP own connection, album uses catalog source. Bind stage to actor/session/authority/audience. No browser track payload accepted. |
| continueImportAction | importId | Same session/actor operation; bounded source requests, trusted staging, declared coverage, progress/failure DTO. |
| publishCollectionAction | importId | Recheck stage/source/authority/current recipients or membership, positive eligible count; atomically create one share. |
| saveTrackFeedbackAction | collectionId, occurrenceId, expectedVersion, scoreHalfPoints, comment | Own eligible reviewer excluding sender; current authorized occurrence; per-track version; score/comment desired state saved together. |
| saveGeneralFeedbackAction | collectionId, expectedGeneralVersion, patch | Own eligible reviewer; optional generalHalfPoints 0..10, favoriteOccurrenceId/leastLikedOccurrenceId UUID or null; at least one effective field. Same-collection/distinct choices; separate general version. |

A replay uses the same operation ID and identical normalized payload; deliberate retry
after VERSION_CONFLICT uses a new operation ID and explicit current expected version.
Wrong audience, Normal playlist submission and Normal OAuth attempts are denied on the
server even if UI controls are bypassed.

General-score field omission preserves the existing manual score. Explicit null manual
score is not a supported vote-delete operation. Optional choices may be cleared/replaced
atomically; omitted choices preserve state. Parent review is created by the first effective
authorized track/general/choice write, with general version zero until general feedback
changes. Track edits never advance that version. No final-submit/reveal gate.

## Invitation receipt and delivery semantics

Invitation receipt includes id, issuedAt, expiresAt and sendStatus: attempting,
smtp_accepted, failed or unknown; sendOutcomeCode is explicitly nullable and uses exactly
the 11 safe values/pairings in [the model](../data-model.md#delivery-outcome-mapping).
The public field is camelCase; its SQL field is send_outcome_code. Only attempting has
NULL; stale attempting is projected as unknown/SMTP_OUTCOME_UNCONFIRMED after the deadline.
The reason is not an HTTP status, raw SMTP number/error or a second activation state.
SMTP_FAILED/SMTP_UNKNOWN are action-envelope categories; sendOutcomeCode explains the
specific receipt reason. English UI messages come from a finite code-to-copy map; no
provider error text reaches the UI. These codes never authorize automatic resend.
Account
activation status is a distinct own-account fact. Only the issuing administrator reads
their receipt through this flow; no new application-wide invitation audit UI is implied.

Generate the random code in the trusted issuance routine and return it once to the
Node sender. The action's UI response contains only the safe receipt. Commit digest/
one-attempt claim before SMTP. Same operation never sends again. Unknown
means possible SMTP acceptance without confirmed finalization; no automatic resend.
Explicit new issuance uses a new operation ID and deadline; do not silently revoke old
codes. A service-only finalizer may record the already-claimed outcome after the admin
session expires, without creating another invitation or changing activation/expiry.

Use Nodemailer 10.6.1, nonpooled verified TLS, no debug/body logging or file/URL attachment
loading, sole intended envelope recipient, and a 15-second AbortController deadline.
Connection/greeting/socket limits fit within that total budget. Require recipient acceptance;
the UI says sending accepted, not guaranteed inbox delivery. A failure/timeout cannot
be presented as activation. Configure Auth and app limits from research.md independently.

## Identity boundary

Signup/sign-in/recovery and invitation email input use the canonical ASCII mailbox policy
in [spec.md](../spec.md#identity-input-and-comparison-policy), with the trusted SQL comparison
specified in [data-model.md](../data-model.md#identity-normalization-and-comparison).
Normalize once before calling Auth; never normalize passwords. Apply the same canonical
email to the request digest, SMTP envelope and trusted verified-email redemption comparison.
Native confirmation never supplies membership/role/tier. Username setup and member search
share the ASCII handle validator: optional single leading @, ASCII letters/digits only,
fixed lowercase and no whitespace/Unicode/special-character rewriting. Do not use it for
email. No request accepts a caller-supplied normalized_username. DTO username is bare
lowercase; shared UI renders @username exactly once. Selection uses UUID, not display text.
No HTTP/Server Action exists for initial administrator bootstrap or operator role changes.

## Authorized read DTOs

| DTO / view | Content and audience |
| --- | --- |
| OwnAccount | Own activation, username, current administrator/common and Normal/VIP authority, safe provider connection availability; no tokens or other account emails. |
| MemberSearch / FollowingList | Activated UUID/username only. Following selection uses UUID, not label matching. |
| Feed / History | Authorized context-specific collection cards: kind, submitter, time, source scope/counts, fresh available title/cover/source and permitted review state. |
| CollectionDetail | Frozen eligible occurrences/exclusions, source coverage, contextual identities and authorized reviews. Sender reads each direct review; direct recipient reads only own feedback. |
| ReviewFeedback | Reviewer identity, track half-points/comments/versions, track mean/progress, manual general score/version and optional choices. Distinct labels and null states. |
| GroupResults | Current member only: group share and authorized individual reviews including retained former-member contributions, separate group manual mean and contributing count. |
| GroupRankings | Current member only: separate playlist/album results, exactly three nullable highlighted slots and remaining ordered rows. Each real entry has share ID, place, grade, contributing count and source scope. |

Safe first-party historical author identity may be returned with an already authorized
record even if no longer discoverable; search still filters activated targets. Never
expose emails, roles or provider identity through author labels.

Direct recipient DTOs expose no co-recipient feedback, aggregate, vote count, existence
signal or list obtained from another reviewer. Administrative authority does not expand
DTO content. Group results/rankings explicitly require group context/current membership;
a generic readable-collection predicate is insufficient.

Derive group means and sequential positions in one authorized query snapshot. Compare
unrounded numeric means then collection UUID. A zero grade is a real ranked entry;
missing mean has no rank. A real entry's stars/text express the same displayed grade,
without rounding it to a manual half-step. Empty highlighted slots contain no fake UUID/
grade. Refresh authorization/revisions before applying late results; never regress a
confirmed revision. Any paging must restart on invalidated view generation rather than
combine stale ranks with current rows.

## Required contract evidence

Cover runtime malformed inputs, role/tier spoofing, source-kind/URL mismatch, unsafe
return paths, all direct/group audiences, removal during mutation/refetch, same-key replay
and conflicting key reuse. Test manual-first and choice-only review creation, zero/null,
cross-collection choices, independent track/general concurrency, stale same-resource
conflicts and actual source cutoffs. Include administrator loss during SMTP finalization,
accepted-but-unacknowledged send, expired native session, unknown delivery, preview without
activation and wrong-current-email redemption. Expanded E2E/application evidence is
implementation work, not a claim of this planning command.
