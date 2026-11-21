# Scope Revision Impact — 2026-11-20

**Active feature**: `specs/001-rate-playlists`.
**Authority**: Revised [spec.md](spec.md), constitution v4.0.0 and the recorded user answers.
**Status**: Revised plan/model/contracts/quickstart and the 136-task execution list are
reconciled for the approved scope. The former 140-task list has been replaced; no
application task, migration, deployment or real email was executed.

## Confirmed scope delta

- Administrator/common profiles replace sole-application-owner invitation authority.
  Administrators register a target email and trigger invitation sending.
- Common users have Normal/VIP import eligibility with equal participation permissions.
  VIP additionally permits a personal Spotify connection; provider eligibility still applies.
- The approved source resolution limits playlist submission to connected VIP owners/
  collaborators. Normal/VIP import albums without a personal connection, with an explicit
  first-100 boundary. Normal participants retain received-playlist review.
- Albums join playlists, sharing the same private collection/review behavior.
- Each reviewer has an independent manual general score as well as the derived track mean.
  Favorite/least-liked choices are added.
- Group score = equal-weight mean of recorded manual general scores; missing is excluded,
  zero is included, and track counts/means do not affect weighting.
- Two group rankings, playlists and albums, use that collective score. Three highlighted
  positions always remain visible, with empty slots when needed and later positions below.

## Post-analysis corrections

The owner requested initial administrator provisioning without an application invitation;
constitution v4.0.0 approves only this initial-installation exception. The operator targets
a verified Auth UUID through protected infrastructure tools; a durable unique receipt
closes bootstrap permanently. No public setup or first-signup privilege is introduced.
Subsequent accounts activate by invitation before any trusted role promotion.

SC-016 now preserves prior activation/invitation state on login failure; US1.20 explicitly
requires VIP for playlist submission. Shared identity-policy examples now define owner-approved @username handles containing
only ASCII letters/digits, canonical lowercase/prefix handling, strict special-character/
Unicode/whitespace rejection, plus separate ASCII mailbox validation and alias
separation. The model enumerates 11 safe SMTP outcome codes and all allowed lifecycle
pairs, including uncertain acceptance and persistence-loss projection. Contracts and
existing tasks are reconciled; two added US1 scenarios bring the total to134.

### Approved analysis remediation

The owner approved correcting analysis findings I1 and U1. T026/T036 now verify only
US1 access, setup and authority facts; browser discovery and actual playlist-submission
denial, including for a Normal administrator, are explicitly owned by T041/T067 in US2.
SC-033 is verified in the appropriate story subsets and completed by integrated release
checks, without claiming that later features already work at the US1 checkpoint.

import_items.exclusion_reason explicitly shares the five-value catalog of
collection_exclusions.reason_code. NULL is allowed exactly for eligible items; ineligible
items require a catalog reason, copied unchanged at publication. The decoder/SQL tasks
cover invalid codes and NULL/eligibility pairs. This clarifies the existing exclusion
contract without adding a reason category or changing import scope.

Task IDs, dependencies, parallel markers, canonical field citations, all 88 functional
requirements, 45 success criteria and 134 acceptance scenarios are preserved. All 136
tasks remain unchecked; this remediation changes planning artifacts only.

## What remains binding

Invitation-only membership after restricted initial administrator bootstrap, verified target-email binding, explicit single-use activation,
24-hour issuance-based expiry, normal returning login and no two-participant cap remain.
Direct feedback stays private to sender/reviewer. Current group membership authorizes all
history; pending/removed members receive none, creator alone administers and any current
member can post. Administrator/VIP roles grant no private-content override. Existing
sender exclusion from reviewing their own submission is retained.

Frozen occurrence identity/order, duplicate handling, half-step track scores, exact derived
arithmetic, current-feedback editing, conflict/retry rules, real-time authorization,
conditional provider retention, zero incremental expense, responsive dark-only PWA and
English repository/UI content also remain.

## Three accepted clarifications

1. For the alternative path, take the first 100 and identify the cutoff.
2. A manual 0–5 half-step collection rating is separate from the track mean; group score
   averages submitted manual ratings and excludes missing ones.
3. Rankings are collective per group, separated by collection type, not personal.

Explicit secondary defaults are labeled A-01–A-09 in spec.md. They are not attributed to
the requester; revisit them if a different role-assignment, choice, tie, contribution or
source policy is desired.

## Reconciliation map

Task IDs below reference the regenerated current list. Historical IDs from the replaced
140-task list are not aliases for the new tasks.

| Area | Current task references | Implementation responsibility |
| --- | --- | --- |
| Roles/invitations | T012, T027, T028, T029, T031, T034, T036 | Trusted authority, verified-email single-use activation and bounded one-attempt delivery. |
| Import methods/albums | T038, T043, T046, T047, T055, T057, T058, T059, T067 | VIP own/collaborative playlists and Normal/VIP first-100 catalog albums, trusted scope/metadata. |
| Manual rating/choices | T068, T072, T073, T076, T078, T080 | Independent feedback/version semantics, same-collection choices and exact track arithmetic. |
| Group collective results | T109, T112, T113, T114, T116 | Current-viewer authorization, retained manual contributions and no direct aggregate. |
| Group rankings | T117, T120, T122, T123, T124 | Separate group/type exact ordering, three highlighted slots, later rows and live reorder. |
| Realtime/revocation | T082, T084, T085, T086, T088, T126 | Own-revision invalidation, native liveness, removal and cross-view convergence. |
| Testing/operations | T125, T127, T128, T129, T131, T133, T134, T136 | Complete local/external/device evidence, free controls, recovery and all-nine-story review. |

## Artifact status

| Artifact | Current use |
| --- | --- |
| spec.md | Current functional source: 9 stories,88 FRs,45 outcomes; approved VIP-playlist/catalog-album resolution recorded. |
| checklists/requirements.md | Revised documentation-quality review, not an application test report. |
| plan.md | Current completed Phase 0/1 plan; all ten design-compliance checks use the approved v4.0.0 initial-admin exception. |
| research.md / import-feasibility.md | Current decisions, initial source failure and approved resolution, dated sources/free caps. |
| compatibility.md | Original core probe plus successful added SMTP strict/build/test/audit evidence and CLI smoke limits. |
| data-model.md / contracts/ | Current23-entity/185-field model and four reconciled interface contracts. |
| quickstart.md | Current setup/validation contract; future application commands explicitly depend on implementation. |
| design-reference.md / design/ | Inspected visual evidence retained; missing surfaces extend the shared responsive dark system. |
| tasks.md | Current 136 unchecked tasks, nine stories, 51 parallel opportunities and all 185 explicit canonical field citations. |
| constitution.md | Updated v4.0.0; restricted initial administrator bootstrap without an application invitation. Existing zero-cost and private-audience obligations remain. |

## Approved source resolution and completed planning

The requester confirmed there is no existing Spotify app. They then explicitly approved:
VIP participants submit their owned/collaborative playlists through personal connection;
Normal and VIP import catalog albums; Normal may receive/review playlists but do not
submit them in this MVP. The first-100 policy remains for catalog albums. This closes
P0-SOURCE by revising scope; no HTML importer or provider bypass is selected.

The revised model/contracts include trusted role/tier authority, hash-only SQL-generated
codes and safe one-attempt SMTP finalization, collection/source coupling, source-specific
purge, durable exclusion provenance, manual-first/choice-only feedback, separate versions
and group-only derived grades/rankings. Native-session/RLS and own-revision invalidation
remain; no administrator override or direct co-recipient aggregation.

The Nodemailer 10.6.1/@types8.0.2 full-stack fixture passes strict checking, seven tests,
bundled-Webpack production build and dated zero-finding audit. Actual provider/mail/
cloud/device application checks remain required implementation evidence. CLI 3.2.0 is
pinned and its local-by-default reset behavior replaces removed --local flags.

Active invitation authority wording now says administrator; historical owner-based
clarifications and the existing Premium governance remain historical/provider context.

## Next workflow

1. Run `$speckit-analyze` across the regenerated list and approved design.
2. Implement the current tasks in their explicit dependency order, keeping all nine
   stories and applicable SQL/privacy/source/mail/mobile gates.
3. Record actual acceptance results and recheck free-plan/operator conditions before
   release. Generation/format coverage alone is not application validation.

Task generation replaced the old list, synchronized status/traceability and clarified
how existing server-only integration helpers are reached without exposing the private
schema. It implemented no app, sent no invitation and created/deployed no cloud resource.
