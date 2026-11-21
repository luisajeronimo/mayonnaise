# UI, Realtime and PWA Contract

**Date**: 2026-11-20 | **Status**: Current Phase 1 design.
**Authority**: [spec.md](../spec.md), [design-reference.md](../design-reference.md),
[http-api.md](http-api.md), [database-rpc.md](database-rpc.md).

## Shared responsive visual system

Use the inspected mobile Figma reference, shared dark tokens and supplied brand masters.
Repository behavior governs functionality. Preserve Space Grotesk headings, DM Sans
body/controls and Space Mono scores/metadata, with consistent type/spacing roles.
Use named Lucide React icons with shared size/stroke tokens, accessible names or
aria-hidden decoration. No alternative icon library, light theme or per-screen palette.

Canvas #121212, cards #252525, inset #1c1b1b, raised #2a2a2a, neutral #393939,
white text, muted #b8b8b8, primary #ffade5, yellow #eaea32, success #7dff95 and danger
#ffb4ab follow the recorded token provenance and foreground pairs. Contrast/focus must
be checked for every actual use, not assumed from a hex value. Prototype inconsistencies
and missing screens are resolved through the design reference, not copied as new rules.

Reuse PrivateShell, CollectionCard, TrackFeedbackRow, ScoreDisplay, RatingControl,
ReviewProgress, SourceCoverage, EmptyState and StatusMessage. Extend those patterns for
administrator invitations, album import, manual feedback/choices, collective results,
RankingPodium and RankingRow. Use one numeric/star renderer across cards/detail/results/
rankings. Artwork remains unaltered in its own region; do not crop/overlay rank labels.

Responsive widths start at 320 CSS px; prototype 390 is a proportion reference. Support
wrapping, 200% text, safe areas, orientation and on-screen keyboard. Interactive targets
are at least 44 by 44 CSS px. No fixed-height text clipping, absolute frame coordinates
or horizontal critical-flow scrolling. Focus, errors and status are perceivable by
keyboard, VoiceOver and TalkBack; stars/colors/position alone convey no essential value.

## Product surfaces and visible states

| Surface | Required behavior |
| --- | --- |
| Signup/login/activation | Separate Auth confirmation and explicit application activation. Invite preview never consumes. Current matching verified email and 24-hour expiry are explained. Initial username setup precedes private use. |
| Own account | Own role/tier and source availability only; Normal cannot connect Spotify or submit playlists. Admin status alone grants neither. |
| Administrator invitation | Recipient email, fixed issuance/expiry and safe attempting/accepted/failed/unknown status. No claim of inbox delivery or automatic activation; explicit new issuance differs from retry. |
| Discovery/following | Activated UUID/bare-username identity selection; shared @username presentation, equivalent bare/leading-@ ASCII-case queries, strict alphanumeric validation, directional follow/unfollow, no email/content leakage. |
| Import/share | VIP own/collaborative playlist connection/full scope; Normal/VIP album URL/first 100 scope. Display progress, exclusions, actual cutoff, failures and source metadata fallback before claiming publication. |
| Feed/history/detail | Authorized context-specific playlists/albums, submitter/source, available cover, frozen eligible occurrences and preserved coverage. |
| Track review | Score0..5 half-steps and optional track comment; zero/unrated distinct, partial/complete progress from track scores only. |
| General feedback | Independent optional manual 0..5 half-step rating, favorite and least-liked eligible occurrence. Track average/manual score/group score have separate labels. |
| Group results | Current member sees authorized reviews/manual mean/contributor count. Retained former-member votes count while lawfully retained. No locked/final-submit implication. |
| Group rankings | Separate playlist/album ranking. Exactly three highlighted positions even when empty, then smaller readable rows 4 onward. Each real place has collection, numeric grade, matching stars and accessible place text. |
| Direct multi-recipient share | Sender sees each permitted review; each recipient sees only own feedback. No co-recipient vote count, mean, review list or existence signal. |

Empty states distinguish no shares, no track score, no general score and insufficient
ranked entries. A real zero score is not an empty rank. Example consistent English labels:
“Track average”, “General rating”, “Group score”, “No general ratings yet”, “Partial”
and “First 100 source items”. Do not adopt a prototype comment counter as a business limit.
No playback controls, tags, public profile feed, push, chat, new role-management or
unapproved delete/edit-submission controls.

Feedback writes show pending until persistence succeeds. Conflict shows current authorized
value/version and preserves an explicit resubmission choice. Failed/stale writes do not
update confirmed averages, progress or rank optimistically. Completion never locks edits.

## Subscription and authorized refresh

Subscribe only to UPDATE on the own participant_sync row. RLS checks own ID, genuine
live native session and active membership; filter is not authority. Event payloads carry
no collection/review/group data. Treat the event as invalidation, not as confirmed review
state or a precise JS bigint source. Realtime may parse numeric bigint as Number;
ignore that value and retrieve the authoritative text revision through get_sync_state.

Use direct Supabase get_sync_state RPC every 2 seconds while foreground/online. It returns
only own authorized revision as decimal text and current access outcome, with no Vercel
Function invocation per poll. It complements Realtime for missed events, session ending
and access loss even when revision is unchanged. RSC/DAL refetch also authorizes anew.

One cycle runs at a time; coalesce notifications but retain one overdue tick while a
cycle is in flight. On completion, immediately run the retained tick instead of dropping
it until another interval. Test a 2.5-second cycle. Capture authentication/view generation
for each request and ignore late results after logout/account switch/removal/newer view.
Revalidate before presenting synchronized state; no stale response may regress confirmed
feedback/ranks. Derived group score/rank comes from the same authorized query snapshot.

Direct feedback invalidates sender and that reviewer only. Group feedback invalidates
current members. Removal invalidates former member once; subsequent group changes never
increment their own revision. An expired/revoked session receives no private payload;
missing/denied liveness result conceals data and revalidates authentication.

## Browser lifecycle, offline behavior and logout

On hidden/background, pause private timers/subscriptions as appropriate and conceal private
views. On visible/pageshow/bfcache restore, validate live access and current state before
revealing content. Connection loss is visible, private writes are blocked and no mutation
queue is persisted. Reconnect fetches authoritative values before declaring synchronization.
Private drafts remain memory-only and must be rechecked against current authorization/
versions; clear them on logout, account switch or permission loss.

Online logout calls the server same-origin POST to revoke the current native session
before deleting the browser SDK's original credentials. The SDK's normal client signOut
can remove local storage before its network call, so it is not the protocol coordinator.
After server acknowledgement, stop channels/timers, clear memory/drafts/router state and
hard-navigate to the public entry. The next account must see no preceding private data.

If offline, conceal immediately and set only a nonsecret pending-signout flag. Preserve
the original credentials until revocation succeeds; block new login/account switching
and private reads/renders. On reconnect, retry server revocation, then clear. Do not claim
a copied JWT is revoked before server confirmation. Coordinate tabs and in-flight Auth
refresh with an authentication generation so late events cannot restore old visibility.

## PWA and caches

Use native manifest plus small authored strict-TypeScript service worker; no PWA plugin,
push/VAPID stack or private offline database. Compile the worker separately with WebWorker
types and include that strict check in CI. Manifest uses dark theme/background, standalone
display and brand-derived install icons; preserve the supplied SVG masters.

Cache only explicit public authored assets/offline shell. Do not intercept/cache private
HTML/RSC/API requests, credentials, Auth/Spotify callbacks, provider metadata or source
payloads. Clear obsolete public caches on activation; version updates must not trap a
client on incompatible assets. Explain a reload/update and protect an unsaved memory draft
without persisting private content. HTTPS installation and installed launch are tested
on actual supported iOS/Android environments.

## Required evidence

Use at least two independent authenticated Playwright contexts for alternating writes
and convergence, plus a third authorized participant and outsider for audience isolation.
Record network/service conditions and end-to-end persistence-to-visible timing across
ten track/general/choice/result/rank/membership changes; target <=5 seconds.
Test separate direct recipients, group removal while mounted, retained-vote visibility,
copied revoked JWT, account switch, background/resume/bfcache, missed/out-of-order events,
overdue fallback, offline logout and pending credentials, partial/general-first feedback,
zero/no-score,0..4 ranked entries and every displayed grade/star equivalent.

Physical Safari/iOS and Chrome/Android install, update, safe-area,200% text, keyboard,
VoiceOver/TalkBack and contrast checks remain separate recorded delivery evidence.
These procedures are required implementation work, not completed by planning.
