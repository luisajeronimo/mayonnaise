# Database RPC and Transaction Contract

**Date**: 2026-11-20 | **Status**: Current Phase 1 design.
**Authority**: [data-model.md](../data-model.md), [spec.md](../spec.md).
Names describe planned versioned SQL functions, not functions already deployed.

## Exposure, identity and roles

Expose public invoker RPC wrappers through Supabase Data API; keep private tables and
implementations outside exposed schemas. Authenticated roles get only safe SELECT/RPC
grants, never raw application INSERT/UPDATE/DELETE. Internal mutation implementations
are owned by a limited NOLOGIN/NOBYPASSRLS non-table-owner role, with precise table grants,
RLS predicates and signed actor identity. Use empty search_path, qualified objects and
explicit/default function-grant revocation in migrations.

A narrow privileged Auth helper checks signed subject, JWT time and session_id against
the actual auth.sessions row and current account_access. Own inactive setup/activation
is the only preactivation exception. Write helper takes native session FOR SHARE, then
account authority FOR SHARE and rechecks current wall clock. Never add a session FK
that prevents native logout, alter managed Auth schema or authorize from editable claims.

Every public routine derives actor/reviewer/issuer from that context, runtime-validates
arguments, and authorizes before lookup/conflict/receipt disclosure. Read helpers used by
RLS return only safe predicates/current-actor facts; no arbitrary Auth row access.
Use security_invoker views or equivalently RLS-respecting queries for aggregates.

## Public RPC families

All mutation families accept p_operation_id uuid unless the protocol is explicitly
one-use source state. p_ prefixes are SQL argument names; fields remain canonical.

| Routine | Principal arguments | Result / transaction invariants |
| --- | --- | --- |
| get_own_account | none | Own minimal setup/activation/role/tier/provider-availability status. |
| get_sync_state | none | Live active own revision cast to decimal text; safe denial if session/access ends, no private context. |
| complete_profile | p_username | Validate bare/one-leading-@ ASCII alphanumeric input; derive the lowercase bare handle/key per model; initialize inactive common/normal access if needed; no role/key arguments. |
| issue_account_invitation | p_recipient_email, p_send_attempt_id | Canonical ASCII recipient per model; active admin plus serialized issuance limits; create hash-only invitation/receipt/sole send claim. Generate the random code inside SQL; fresh issuer response returns it once to Node, never through a stored receipt/replay. |
| get_invitation_receipt | p_invitation_id | Issuing administrator and live authority; safe effective status/outcome pair per model, including stale-attempt projection; no raw transport errors. |
| redeem_account_invitation | p_code | Canonicalize current trusted verified email under lock; count attempts, reject already-active accounts without consuming another code, atomically consume/activate before strict expiry. |
| search_members | p_query | Bare/one-leading-@ ASCII alphanumeric literal substring query per model; empty/lone @ gives no results, other invalid characters are rejected; activated UUID/bare username only, never email. |
| follow_participant / unfollow_participant | p_target_id | Own directional pair; no old-grant/group cascade. |
| create_group | p_name | Group plus creator membership atomically. |
| invite_group_member | p_group_id, p_target_id | Creator follows active target; one pending invitation. |
| accept_group_invitation | p_invitation_id | Target-only single membership; old accepted invite never readmits after removal. |
| remove_group_member | p_group_id, p_target_id | Creator only; revoke current group, cancel old pending invites, retain feedback/direct grants. |
| publish_collection | p_import_id | Actor/session-owned attested stage; source-specific eligibility, declared coverage, audience and positive count; one collection per publication operation. |
| save_track_feedback | p_collection_id, p_occurrence_id, p_expected_version, p_score_half_points, p_comment | Own eligible review, same-collection occurrence, independent per-track replacement version. |
| save_general_feedback | p_collection_id, p_expected_general_version, p_patch | Own general score/choices, same-collection/distinct choices, separate general-feedback version. |
| get_collection_detail | p_collection_id | Current authorized frozen structure, fresh metadata and pair/group-safe feedback. |
| get_group_results | p_group_id, p_collection_id | Explicit same-group share and current viewer membership; authorized individual reviews/manual mean/count. |
| get_group_rankings | p_group_id | Current viewer membership; two type partitions, three nullable highlighted slots each, later rows. |

Feed/history/follow/group reads may use safe SELECT/DAL queries where no composite
projection RPC is necessary. No generic arbitrary-SQL RPC or broad service-role query
is introduced. Read contracts are in [http-api.md](http-api.md).

Expected-domain failures return typed safe outcomes so attempt counters and appropriate
receipts can commit. Do not raise an exception that rolls back denied redemption counters.
Unexpected integrity/service failures roll back the mutation. Transport failure is not
confirmation. Conflict payloads reveal current values only after current authorization.

## Narrow server-only integration operations

Node adapters reach private implementations through narrow public integration_* RPC
wrappers with EXECUTE granted only to service_role, never PUBLIC, anon or authenticated.
Keep private outside Data API exposure. These entries provide no generic table/SQL
access or business DAL authorization. Validate the stated actor/session/stage or existing
SMTP claim; ordinary publication/group/review/read routines retain user JWT and RLS.

| Private operation | Scope and constraints |
| --- | --- |
| begin/consume Spotify authorization | Verified live VIP/session, state digest, encrypted PKCE, authority version and connection generation; no arbitrary subject parameters. |
| read/refresh encrypted personal grant | Own attested actor context; bounded refresh lease and generation/version CAS; no network within DB transaction. |
| attest/advance collection stage | Server-validated provider response only; bind actor/session/operation/source/scope/authority and ordered positions. Browser cannot mark a stage ready. |
| finalize_invitation_send | Existing invitation and send_attempt_id, exact permitted status/outcome pair from the model; may finish after issuer session/role changes. No issuance, activation, deadline mutation, second send or arbitrary messaging. |
| cleanup/revoke source data | Exact applicable origin/generation or explicit erasure scope; invalidate before purge, never convert personal origin to catalog. |

Initial bootstrap and subsequent authority maintenance are not integration_* operations.
They use a separate protected operator administration connection and versioned private SQL
routines, with no EXECUTE grant to PUBLIC/anon/authenticated/service_role. Bootstrap follows
the model's verified-UUID, no-prior-activation and unique durable receipt constraints;
a same-identity retry returns the receipt without reapplying authority. Maintenance can
change role/tier for an already activated identity but cannot activate a new member.
There is no public setup route or application invitation for the initial administrator.

The runtime service-role credential is confined to these integration/lifecycle helpers.
Ordinary business reads/writes and final publication remain user-JWT/RLS operations.
Private mail finalization is necessary because external SMTP acknowledgement cannot
share the originating Auth transaction. Its arguments must match an existing claimed
attempt; arbitrary accepted statuses from a browser are never trusted.

Catalog attestation has source_mode=catalog_album, kind=album, requested_scope=first_100,
and NULL personal connection/generation/snapshot. Connected attestation requires
source_mode=connected_playlist, kind=playlist, full scope, actor VIP, own current
connection/generation/authority version and stable playlist snapshot. Both bind a logical
initiating native session, validated audience, 24-hour stage expiry and exact source-
position coverage. No fake album snapshot or borrowed grant.

## Locking and retry protocol

Activation first locks the current auth.users actor row FOR SHARE to serialize current
verified-email changes; the privileged helper may lock only the signed-in actor.
Then use the common order: native session -> account_access -> relevant connection -> issuance
advisory gate if applicable -> group -> invitation/membership -> review/import and
feedback -> participant_sync rows in sorted UUID order. Use only needed locks.

Group membership administration uses group FOR UPDATE; posting/review uses group FOR
SHARE then current membership FOR SHARE. Native session deletion and authority changes
take conflicting locks. Checks after waits use clock_timestamp, not transaction-start
time. Read committed snapshots are acceptable with these explicit write locks and
post-lock rechecks; no network request runs inside the transaction.

Issuance takes a transaction-scoped project advisory lock before checking both counters:
five issuance operations/admin/hour and 20/project/rolling 24 hours. Count attempts conservatively;
a concurrent different admin cannot exceed the project bound through count-then-insert.
Same-key replay does not issue/send/count another invitation. Auth SMTP has its own
10/hour project-wide configuration; app SMTP does not inherit it.

Operation receipt PK is (actor_id,operation_id), with kind and canonical request digest.
Hash the validated caller's semantic payload, excluding generated code, send-attempt UUID,
timestamps and provider response facts. Check replay before generating a new code/attempt;
a repeated issuance returns only the original receipt, never another send claim.
Identical retry returns the currently authorized result. A changed payload/kind under the
same key is OPERATION_CONFLICT. Store only safe result identifiers/status, no raw code,
message body, provider token or stale private DTO. Cleanup must preserve needed replay
barriers; expiring a receipt cannot make old publication/send operations execute again.

Publish stage locks ensure continuation retries replace the same original position.
Unique sender/publication operation and import publication pointer return one collection.
Recheck current follows for new direct recipients and current membership for group posting
at publication; no silently expanded/changed audience. No completed share with zero
eligible occurrences or unproven declared scope.

## Feedback, aggregates and rank consistency

First effective track/general/choice save inserts/finds one unique parent review and
locks it. Track save checks expectedVersion 0 for first occurrence or current stored
version for replacement; increment only that track version. General patch checks
general_version, updates general score/choices atomically and increments only general
version. Track-first parent version remains 0. Same-resource stale edits reject visibly;
unrelated track/general edits do not create artificial conflicts.

Validate composite review/occurrence/collection FKs, sender exclusion, exact integer
0..10 scores and nullable distinct choices. Omitted patch fields preserve state.
Manual null does not delete a recorded vote. Choice null can clear optional selection.
Comment-only/choice-only edits cannot alter numeric grades or track progress.

Group projection authorizes the current viewer, then includes lawfully retained review
authors even if their membership is now removed. Exclude absent manual scores, include
zero, equal weight. No direct share, other group or other context contributes. Use
numeric numerator/denominator with adequate pre-display precision (numeric(80,40)
numerator), then ROW_NUMBER ordered by unrounded mean DESC, collection UUID ASC within
group/type. Display-only rounding is nearest hundredth/upward ties. Means/counts/ranks
come from one authorized statement snapshot. No stored score/rank rows.

Every successful mutation updates computed authorized revision audiences transactionally.
Direct feedback touches sender/reviewer; group feedback touches current members; removal
invalidates former member once. Later group mutations do not touch former-member rows.
No caller can choose a fanout audience or update revisions directly.

## Publication, lifecycle and required evidence

SQL migrations own RLS, grants, roles, indexes, composite constraints, safe helpers,
RPCs, participant_sync UPDATE publication and bounded cleanup jobs. Exclude private
payload tables from Realtime publication. Provider reference revocation/expiry hides
metadata but leaves internal occurrence/progress structure.

Run SQL tests with genuine signed-session contexts as well as adversarial claims:
anonymous/inactive, copied revoked JWT, expired session, wrong subject/session, role/tier
spoofing, raw REST DML bypass, wrong direct recipient, removed/pending group member,
foreign occurrence, sender self-review and aggregate leakage. Include admission/removal/
publication races, activation at the exact deadline, denied-attempt persistence, native
logout/write serialization, VIP downgrade during provider work, failed SMTP finalization
and idempotent retries.

Rebuild empty SQL history and apply forward changes from the prior supported fixture.
Test restoration without reviving sessions, consumed invitations or obsolete authority/
provider generations. These are required implementation checks, not results achieved
by this design document.
