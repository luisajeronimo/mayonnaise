# Feature Specification: Mayonnaise — Share and Review Spotify Playlists and Albums

**Feature Branch**: `main` — existing branch; this revises the active feature.
**Feature Directory**: `specs/001-rate-playlists`
**Created**: 2026-11-18
**Revised**: 2026-11-20
**Status**: Specified — primary clarifications and the import-scope resolution are recorded.
Current technical design and 136-task execution list are reconciled; consistency analysis
is the next workflow before implementation.

**Input**: A private, invitation-only mobile application for sharing Spotify playlists and
albums, evaluating occurrences and whole collections, and comparing collective group
results. Administrator profiles send email-bound activation invitations. Common profiles
have Normal or VIP import eligibility. Only VIP users may connect a personal Spotify
account and submit eligible owned/collaborative playlists. Normal and VIP users may
import Spotify albums from a link without a personal connection, with a disclosed
first-100 boundary. Normal users retain receiving and reviewing shared playlists.
Only the initial administrator is provisioned by a trusted operator without an application
invitation; every subsequent account retains invitation activation.

Track-based means and manual general ratings are distinct. Group final scores average
recorded manual general ratings equally, excluding missing values. Each group has separate
playlist and album rankings with three persistent highlighted positions and smaller rows
below. Favorite/least-liked choices and all earlier private direct/group, activation,
history, real-time, accessibility, responsive mobile and dark-only obligations remain.

**Governance**: [Constitution v4.0.0](../../.specify/memory/constitution.md).
This revision requests no paid infrastructure, credential sharing, public social access,
or exception to provider conditions. The existing developer-owner Premium exception
does not determine application administrator/VIP authority.

**Downstream status**: [change-impact.md](change-impact.md) records the approved scope and
reconciled plan, model, contracts, quickstart and current [tasks.md](tasks.md). The former
140-task list is replaced; no application feature has been implemented by these documents.

## Clarifications

Earlier sessions are a historical decision record. The 2026-11-20 scope revision below
supersedes sole-owner/manual-only invitation issuance, playlist-only/connected-only
imports, the absence of manual overall ratings, and the exclusion of collective group
scores and rankings. Current functional requirements and confirmed decisions govern.

### Session 2026-11-19

- Q: How should someone become one of the two authorized Mayonnaise participants? → A: There is no two-participant business limit; participants need a private invitation and a single-use code to activate their account.
- Approved design (user-requested research): The application owner issues a private account-activation code or invitation link; the recipient signs in and explicitly activates their account. Activation and code consumption succeed together, the code cannot be reused, and subsequent sign-ins do not require another invitation. Store only protected one-way verification data and limit guessing attempts. Invitations expire 24 hours after issuance and are bound to the intended recipient's verified email, as defined by the accepted clarifications below.
- Q: Who should be able to see and rate a shared playlist? → A: Participants search for people by username in an in-app menu and follow them. Following adds that person to the follower's sharing list without requiring a reciprocal follow. A playlist can be shared directly with one or more selected people from that list or with a previously created private group; following alone does not expose private playlists.
- Q: When sharing with several people, who should see each recipient's ratings and comments? → A: For direct sharing, only the sender and each individual reviewer see that review. Participants can also share with a private group created beforehand, where all group members can see the reviews.
- Q: How should people join a private group you create? → A: Only the group creator manages membership and invites people they follow. Each invited participant must accept the group invitation before becoming a member. Group invitations are separate from account-activation codes.
- Q: When people join or leave a group, how should their access to earlier playlists and reviews change? → A: Current group members can read the group's entire history, including earlier playlists and reviews. Removed members lose group access. Independent direct-share permissions remain separate.
- Governance resolution (user-requested): Constitution v3.0.0 resolves DP-01 by permitting the application owner's already-held personal Spotify Premium subscription as a narrow external prerequisite. MVP infrastructure and incremental project spending remain zero; no new subscription, upgrade, paid API access, or other paid dependency is authorized.
- Approved integration direction (user-confirmed): Start the automatic Spotify import pilot with up to five allowlisted Spotify accounts. Keep Mayonnaise membership rules separate from this external limit. Broader automatic imports require Spotify-approved access; user-supplied tracklists are a possible future scope change, not an added MVP requirement.

- Q: Should an account activation code work only for the person you invited? → A: Only the intended recipient can redeem it, using the verified email address specified by the application owner when issuing the invitation.

- Q: How long should an unused account activation invitation remain valid? → A: 24 hours from issuance; an unused invitation is invalid at or after that expiry time.

- Q: Who should be able to share a playlist with an existing private group? → A: Any current group member can share a playlist with the group. Only the group creator manages membership.

- Q: When you unfollow someone, should they keep access to playlists you already shared directly with them? → A: Keep access to existing direct shares and reviews. Unfollowing removes the person from eligibility for new direct shares until followed again.

- Q: When should participants be allowed to edit their own saved track ratings and comments? → A: Participants may edit their own saved track ratings and comments at any time, including after completing a playlist review, while they remain authorized for the share. Confirmed score changes recalculate the playlist average automatically.

### Session 2026-11-20

- User-provided visual direction: Use the mobile Figma prototype faithfully while keeping
  this repository as the source of truth for behavior and scope. Use dark mode only,
  shared typography/colors/icon treatments, reusable components/layouts, and the supplied
  logo/favicon. Extend the same visual language to required screens absent from the
  prototype. Reference provenance and access verification are in
  [design-reference.md](design-reference.md); uninspected frames are not verified evidence.
- Responsive layout clarification: Prototype dimensions are visual references, not fixed
  implementation requirements. Adapt layouts to mobile widths, orientation, text
  enlargement, safe areas, and the on-screen keyboard while preserving visual identity.

### Scope Revision 2026-11-20

- The requester introduced administrator/common-user profiles. Administrators enter an
  email and send an activation invitation; common users cannot issue them.
- Common users have Normal or VIP import eligibility. Participation features and private
  permissions are equal; VIP additionally allows connecting a personal Spotify account.
  Normal users need a link-based import path without that connection.
- Spotify albums join playlists as shareable/rateable collections, with the same private
  direct/group behavior and independent imported occurrence identities.
- Manual overall collection ratings and favorite/least-liked choices are added alongside
  the existing individual track scores and derived track mean.
- Group collective results and separate group rankings for playlists/albums are approved.
  Each ranking has three persistent highlighted positions and smaller remaining rows.
- Q1: How should alternative imports handle more than 100 tracks? → A: Import the first
  100 and explicitly identify the cutoff.
- Q2: How do manual and derived grades relate? → A: Keep a separate manual 0–5 half-step
  general rating; the group score is the simple mean of recorded manual ratings, excluding
  people without one. The track mean remains separate.
- Q3: Are rankings personal or collective? → A: Rankings are per group and use the
  collective final score; playlists and albums remain separate.
- Research observed artwork/title preview metadata and visible ordered tracks/artists/
  durations in public examples. This is not proof of a stable or permitted production
  importer or a universal provider limit. See [import-feasibility.md](import-feasibility.md).
- The existing technical plan, contracts, field model and 140 tasks predate this revision
  and must be reconciled before implementation; no application code is changed here.

### Approved Import Scope Resolution 2026-11-20

- Q: Does Mayonnaise already have a Spotify Developer Dashboard application? → A: No.
  Planning must use current new-application access, not assumed older entitlements.
- The requester explicitly approved this MVP adjustment after the source limitation was
  explained: only VIP participants with their own authorized Spotify connection submit
  playlists; Normal participants may import albums and review received playlists.
- Normal and VIP participants both use the non-linked catalog album path. The accepted
  first-100 cutoff remains for this album path; it is not a workaround for public
  playlist enumeration. Connected playlists retain full declared-scope pagination.
- This decision supersedes earlier requirements for Normal link-only playlist imports.
  Private audience, group participation, feedback weights, ranking and mobile rules
  remain equal under current authorization. No manual/file fallback is added.

### Approved Post-Analysis Corrections 2026-11-20

- The owner requested provisioning the initial administrator without a Mayonnaise
  invitation. Constitution v4.0.0 authorizes only this restricted initial exception.
  A trusted operator selects an already verified Auth identity before ordinary account
  activation. There is no public setup screen, first-signup privilege or invitation-free
  path for subsequent accounts, including accounts later promoted to administrator.
- A failed login preserves the account's prior activation state and all invitation
  consumption/expiry facts. Incorrect credentials never deactivate an active account.
- Playlist submission remains VIP-only; Normal and VIP retain album import and the same
  authorized participation features. Administrative role alone grants no VIP capability.
- The identity policy below defines case, whitespace, Unicode and email comparison.
  The planning contracts enumerate safe SMTP outcomes separately from sending state.
  These are explicit design decisions adopted in this review, not earlier user answers.
- Subsequent owner correction: usernames are unique alphanumeric handles displayed as
  @username, not free-form Unicode names. This supersedes the earlier printable-Unicode
  username policy. Only ASCII letters and digits are permitted; no underscore or other
  special character is allowed inside the handle. The email policy is unchanged.

## User Scenarios & Testing *(mandatory)*

Priorities indicate delivery order, not exclusions: all nine stories are in the revised
MVP. Prepared participants, source samples and feedback allow each story to be verified
independently. A collection is one context-specific playlist or album submission with a
frozen imported evaluation scope, not a global record merging private sharing contexts.

### User Story 1 - Access a Private Space and Administer Invitations (Priority: P1)

As an invited participant, I want an identified private account. As an administrator,
I want to enter a recipient email and send a private activation invitation. Common
users have Normal or VIP eligibility. Only VIP may connect Spotify and submit eligible
playlists; both may import albums, participate in groups and review authorized shares.

**Why this priority**: Privacy and identification are prerequisites for all interactions.
Addresses principles IV and VII.

**Independent Test**: Provision the initial administrator without an application invitation,
then prepare at least three invited users including Normal and VIP,
unverified/inactive/outsider identities, and private records. Verify all activation,
verified-email, expiry/concurrency, role-authorization, delivery-status and session-isolation
cases without treating VIP as a paid plan or a Spotify authorization.

**Acceptance Scenarios**:

1. **Given** a participant activated through a private invitation is unauthenticated, **When** they
   complete access with valid identification, **Then** they see their authorized private space and their identity.
2. **Given** an unauthenticated, inactive, or unauthorized person, **When** they attempt to access
   a collection, rating, or update from the space, **Then** no private data is displayed.
3. **Given** an authenticated participant, **When** they submit a collection or a rating,
   **Then** authorship matches their identity and cannot be attributed to another participant.
4. **Given** an ended or expired session, **When** the application is reopened,
   **Then** access is required again and no private content from the previous session is revealed.
5. **Given** a valid, unused code and a signed-in account whose verified email matches
   the invitation's intended recipient, **When** the recipient explicitly activates their
   account, **Then** the account is activated and the code is consumed by that activation.
6. **Given** a missing, invalid, expired, or previously used activation code, **When** a person attempts
   activation, **Then** activation is rejected and no private content becomes accessible.
7. **Given** concurrent attempts to redeem the same unused code, **When** activation is
   processed, **Then** at most one eligible account activates, all attempts by accounts
   without the matching verified recipient email are rejected, and no duplicate activation occurs.
8. **Given** more than two participants each have a separate valid private invitation,
   **When** they activate their accounts, **Then** a third account is not rejected merely
   because two participants are already active.
9. **Given** an administrator, **When** they enter an intended recipient email and send
   an invitation, **Then** an email-bound single-use invitation is created and delivery
   is requested only to that recipient; the interface distinguishes confirmed sending
   from failure, and sending alone does not activate the account.
10. **Given** a common user or unauthenticated person, **When** they attempt to
    issue an account-activation invitation, **Then** creation is denied and no activation code is disclosed.
11. **Given** a valid unused invitation link, **When** it is opened or previewed without
    explicit activation, **Then** the code remains unused and no account is activated.
12. **Given** an unused invitation and an unauthenticated recipient, **When** they try to
    activate an account, **Then** identity verification is required before activation;
    a failed sign-in leaves the invitation unused.
13. **Given** an inactive signed-in recipient and a valid unused invitation, **When** activation fails
    before it completes, **Then** the account remains inactive and the code remains unused,
    allowing a retry while the invitation is still valid.
14. **Given** an account already activated by invitation, **When** its participant signs in
    again or on another device, **Then** ordinary authentication grants authorized access
     without requesting or consuming another invitation. An incorrect password rejects
     only that login; the account remains active, its consumed invitation stays consumed,
     and a later correct login succeeds without another invitation.
15. **Given** the configured activation-attempt limit has been reached, **When** additional
    attempts occur during the restricted period, **Then** they are denied without activating
    accounts or consuming a valid invitation.
16. **Given** a generated invitation, **When** persisted verification data and application
    logs are inspected, **Then** they do not disclose the original usable activation code.
17. **Given** a valid unused invitation addressed to a different email, **When** a signed-in
    account tries to redeem a forwarded code, **Then** activation is denied and the invitation
    remains unused for its intended recipient.
18. **Given** an account whose email matches the invitation but is not verified, **When** it
    attempts activation, **Then** activation is denied until email verification succeeds;
    the denied attempt does not consume the invitation.
19. **Given** an unused invitation issued 24 hours earlier, **When** the intended recipient
    attempts activation at its expiry time or later, **Then** activation is rejected without
    consuming the code or exposing private data. Opening the link earlier does not extend
    its lifetime; an otherwise valid activation before the deadline can succeed.

20. **Given** a common Normal user and a common VIP user, **When** their permissions are
    compared, **Then** both may import/share albums and use the same authorized group,
    review, result, ranking and mobile features. Only VIP may connect personal Spotify
    and submit eligible playlists; a Normal playlist-submission attempt is denied even
    when the caller also has administrative authority.
21. **Given** a common user submitting a forged administrator or VIP designation,
   **When** the request is processed, **Then** no administrative authority or connection
   privilege is granted.
22. **Given** an invitation delivery failure, **When** the administrator sees the outcome,
   **Then** no successful delivery or activation is claimed, the code is not consumed,
    and retrying does not silently create duplicate accounts or extend the original deadline.
    Every sending state/reason pair follows the enumerated delivery contract; a lost final
    acknowledgement is unknown, not a definite failure or an automatic resend instruction.
23. **Given** an installation with no prior activated account and an explicitly selected,
    verified Auth identity, **When** the trusted operator provisions the first administrator,
    **Then** that identity receives an active administrative profile without creating,
    sending or consuming an application invitation. Repeating the operation for that
    identity has no new effects; another identity, an unverified account or an ordinary
    application caller cannot use it to bypass subsequent invitation activation.
24. **Given** the identity examples below, **When** signup/profile setup, administrator
    invitation entry, member search and verified-email redemption are checked, **Then**
    equivalent inputs use the same canonical identity, conflicting usernames are rejected,
    different email aliases remain different, unsupported email inputs are rejected,
    and member search exposes no email addresses.

---

### User Story 2 - Find People and Share Playlists or Albums Privately (Priority: P1)

As a participant, I want to share Spotify playlists or albums with selected people I
follow or a previously created private group. VIP users may connect their own Spotify
account for eligible connected playlist imports. Normal and VIP users submit album
links through the non-linked catalog path, which imports at most the first 100 source
occurrences and discloses a cutoff when applicable. Normal users may receive/review
playlists but cannot submit them in this MVP. Current group members can share
multiple albums and playlists in the same group; only the creator administers membership.

**Why this priority**: Provides the content for the experience and avoids manual track entry.
Addresses principles I, III, IV, and VII.

**Independent Test**: Prepare Normal/VIP senders, two selected followed recipients, an unselected followed
user, an outsider, a group creator/current non-creator member, pending invitees and removed
members. Use VIP connected playlist links, Normal/VIP non-linked album links, rejected
Normal playlist attempts,
more-than-100-track sources, unavailable metadata and multi-part imports.

**Acceptance Scenarios**:

1. **Given** an authorized sender, selected followed recipients, and an eligible
   playlist or album for the chosen import path, **When** import and sharing finish,
   **Then** only the authorized audience finds the collection, source link, authenticated
   submitter and the frozen imported occurrence set with its declared coverage.
2. **Given** a source delivered in multiple parts, **When** import finishes, **Then**
   all parts needed for the declared scope have been considered. A connected full import
   must not omit later pages; a non-linked capped import must disclose that it is limited
   to the first 100 rather than claiming the complete original.
3. **Given** an invalid link, another content type, or a collection without read permission,
   **When** the participant attempts to share it, **Then** they receive an explanation
   and no incomplete list is announced as successfully imported.
4. **Given** a submitted collection, **When** an authorized participant opens its link,
   **Then** listening takes place in the external service, without playback inside Mayonnaise.
5. **Given** an imported collection, **When** its track list changes on Spotify,
   **Then** the list used for that evaluation remains the one captured at submission time.
6. **Given** a collection with two importable tracks and two incompatible items,
   **When** import finishes, **Then** the two track occurrences can be rated,
   the two excluded items are indicated, and the total number of rateable occurrences is two.
7. **Given** an empty collection or one with no importable tracks, **When** the participant
   attempts to share it, **Then** submission is rejected with an explanation and does not
   create a rateable collection in the feed.
8. **Given** a collection imported from Spotify, **When** authorized participants view its
   information, **Then** they can identify Spotify as the source and open the corresponding content.
9. **Given** a sender following multiple people, **When** they select one person for a direct share,
   **Then** only the sender and that recipient are within its authorized audience.
10. **Given** a sender who selects two or more people they follow, **When** the direct share completes,
    **Then** every selected recipient can access the collection and can submit an eligible review.
11. **Given** an activated participant outside the share, including a non-selected followed
    person or someone following a recipient, **When** they try to find the share in a feed,
    history, a direct link, or live updates, **Then** no private collection or review information
    is disclosed to them.
12. **Given** an attempt to select a new direct recipient the sender does not follow,
    **When** sharing is requested, **Then** that recipient is rejected and access is not granted.
13. **Given** a forwarded Mayonnaise link, **When** someone outside the selected audience
    opens it, **Then** the link does not grant access to the private share.
14. **Given** an activated participant and another member's username, **When** they search
    in the application's search menu, **Then** they can identify the matching member and
    follow them without seeing private collection or review data in the search results.
15. **Given** participant A follows participant B and B does not follow A, **When** A chooses
    collection recipients, **Then** B appears in A's sharing list and can be selected.
16. **Given** B follows A but A does not follow B, **When** A views their sharing list,
    **Then** B is not eligible solely because B follows A.
17. **Given** an existing follow from A to B, **When** A repeats the same follow action,
    **Then** B appears once in A's following and sharing lists.
18. **Given** an activated participant, **When** they create a private group, **Then** the
    group has its own identity and a recorded creator, distinct from administratorship.
19. **Given** a previously created private group containing its creator and other current
    members, **When** any current member, including a non-creator, shares an eligible
    collection to that group, **Then** authorized group members can access the collection and
    its group-visible reviews, while non-members cannot.
20. **Given** a group creator who follows an activated participant, **When** the creator
    invites that participant, **Then** an invitation addressed to that account is pending;
    the account does not become a member automatically.
21. **Given** a non-creator member, outsider, or administrator who is not the group
    creator, **When** they attempt to invite or remove members, **Then** the action is denied.
22. **Given** a pending group invitation, **When** its recipient tries to access group
    collections or reviews before accepting, **Then** the invitation alone grants no access.
23. **Given** the authenticated intended invitee, **When** they accept a valid pending
    invitation, **Then** they become a group member and can access the group's existing
    history and future shares under the group sharing rules.
24. **Given** an account other than the intended invitee, **When** it tries to accept the
    invitation, **Then** no membership is granted.
25. **Given** repeated or concurrent acceptance of the same group invitation, **When**
    acceptance succeeds, **Then** the invited account has only one membership in that group.
26. **Given** a group invitation for an already activated account, **When** it is accepted,
    **Then** no account-activation code is consumed and no separate account is activated.
27. **Given** a group creator who does not follow a prospective invitee, **When** they try
    to issue a new group invitation to that account, **Then** the invitation is rejected.
28. **Given** a group containing earlier collections and reviews, **When** an invited person
    accepts and joins, **Then** they can read that earlier group content as a current member.
29. **Given** a current group member, **When** the creator confirms their removal,
    **Then** that person loses access to all group collections and reviews, including
    earlier content and reviews they authored.
30. **Given** a removed group member who also has independent direct shares, **When** they
    access those direct shares, **Then** their independent permissions are unchanged and
    the group's historical reviews remain available to its current members.
31. **Given** a VIP account without required provider authorization or allowlisting,
    **When** it attempts a connected import, **Then** the external limitation is explained
    and no incomplete connected import is published; application VIP status does not
    grant Spotify permission or disable the separate non-linked album path.
32. **Given** a pending invitee, removed member, or other non-member, **When** they attempt
    to share a collection with the private group, **Then** posting is denied and no group
    submission is created; administratorship alone does not permit posting.
33. **Given** participant A previously shared a collection directly with participant B,
    **When** A unfollows B, **Then** B leaves A's list for new direct shares, while both
    retain their existing direct-share permissions and reviews. A cannot choose B for a
    new direct share until following B again; any reverse follow or group membership is unchanged.

34. **Given** a Normal user without a connected Spotify account and an eligible Spotify
    album, **When** they share its link through the catalog path, **Then** the declared
    import succeeds without asking them to connect a personal Spotify account.
35. **Given** an album source with 120 eligible ordered occurrences, **When** the catalog
    import completes, **Then** the first 100 are the frozen evaluation set and the cutoff
    is prominent; the remaining 20 are not silently represented as imported or rated.
36. **Given** an eligible album link, **When** it is imported, **Then** its title, available
    cover/artist data and ordered rateable occurrences are available under the same
    sharing/review rules, without requiring the participant to own or collaborate on
    the album as if it were a playlist.
37. **Given** missing artwork or optional source metadata, **When** the collection is
    viewed, **Then** missing values use understandable placeholders and do not fabricate
    a cover, artist, duration, original total or verified source ownership.
38. **Given** a group with multiple playlists and albums, **When** another current member
    submits an eligible collection, **Then** it appears in that same private group as its
    own context-specific share; existing reviews are not merged from other contexts.
39. **Given** unavailable or insufficiently verified album catalog data, **When** a
    non-linked import cannot establish its promised scope, **Then** it fails or remains
    incomplete with an explanation rather than publishing a falsely complete tracklist.
40. **Given** a Normal user submitting a playlist link, **When** the submission is requested
    through any client or direct endpoint, **Then** it is denied with an explanation,
    without creating a share or borrowing a VIP participant's Spotify authorization;
    their album-import and authorized received-playlist review permissions remain available.

---

### User Story 3 - Rate Tracks and the Whole Collection (Priority: P1)

As an eligible reviewer, I want to score individual occurrences, see their track-based
mean and progress, and give an independent manual overall rating to the playlist or album.
I can choose a favorite and a least-liked occurrence and edit my own feedback while I
remain authorized, including after completing track ratings.

**Why this priority**: This is the product's core function. Addresses principles IV, V, and VII.

**Independent Test**: Prepare direct/group playlist and album shares with independent reviewers. Verify the
existing occurrence arithmetic and edit/privacy cases, then set a manual general rating
that differs from the track mean and select occurrences belonging to that same share.

**Acceptance Scenarios**:

1. **Given** a collection with no ratings, **When** the recipient opens it, **Then** they see
   that it has not been rated; the absence of a rating is not displayed as a zero score.
2. **Given** a collection with three distinct tracks, **When** the recipient saves a score
   of 2.5 for the first track, **Then** the displayed average is 2.5, marked as partial,
   with one of three tracks rated.
3. **Given** the previous scenario, **When** the recipient saves a score of 3 for the second
   track, **Then** the partial average becomes 2.75, with two of three tracks rated.
4. **Given** the previous scenario, **When** the recipient saves a score of 5 for the third
   track, **Then** progress shows three of three and the evaluation is complete;
   the average is 10.5 divided by 3, which is 3.5.
5. **Given** the submitter or someone outside the authorized sharing context, **When** they attempt to record a rating
   for that collection, **Then** the action is denied and the ratings remain unchanged.
6. **Given** a track or manual general score outside 0 to 5 or outside 0.5 increments, **When** the person attempts
   to save it, **Then** they receive a validation message and no invalid rating is recorded.
7. **Given** a collection with two occurrences of the same track, **When** the recipient
   saves 4 for one occurrence and 2 for the other, **Then** both count separately,
   progress is two of two, and the completed average is 3.
8. **Given** four occurrences with scores of 0, 0, 0, and 0.5, **When** the average is displayed,
   **Then** the average of 0.125 is shown as 0.13 without changing the original scores.
9. **Given** a direct share sent to reviewers B and C, **When** B saves a score or comment,
   **Then** only B and the sender can see B's review and its average and progress;
   C can access the collection and C's own review without seeing B's feedback.
10. **Given** a collection shared with a private group, **When** a group reviewer saves a score
    or comment, **Then** authorized group members can read that review with its author,
    average, and progress, but cannot submit scores on that reviewer's behalf.
11. **Given** a completed review with track scores of 2 and 4, **When** its authorized
    reviewer changes the first score to 5, **Then** the confirmed average becomes 4.5,
    progress remains two of two, and the review remains complete without a duplicate rating.
12. **Given** a partial review with one of three occurrences rated 2.5, **When** its reviewer
    changes that score to 0, **Then** the average becomes 0, remains partial, and progress
    remains one of three. An invalid replacement leaves the saved score and average unchanged.
13. **Given** a saved rating, **When** another reviewer, the collection sender, an application
    administrator without reviewer eligibility, or a removed group member attempts to edit it,
    **Then** the edit is denied and the confirmed rating and summary remain unchanged.

14. **Given** a reviewer whose track mean is 4.5, **When** they save a manual overall rating
    of 2, **Then** both are displayed with distinct labels; the track mean stays 4.5 and
    the collection rating is 2.
15. **Given** no manual collection rating, **When** the reviewer saves 0, **Then** zero is
    a recorded general rating, distinct from unset, and participates in the group score.
16. **Given** imported rateable occurrences, **When** the reviewer chooses a favorite and
    a least-liked occurrence, **Then** each choice belongs to that reviewer and this
    collection's frozen list, under the same feedback-visibility rules.
17. **Given** saved general feedback and current authorization, **When** its reviewer edits
    the manual score or choices, **Then** confirmed changes persist without creating
    another reviewer or changing the number of rated tracks.
18. **Given** another share's occurrence, another reviewer, a sender without review
    eligibility or a removed group member, **When** they attempt to save these choices
    or a general score, **Then** the write is denied without disclosing private feedback.
19. **Given** a general-score edit, **When** it is confirmed, **Then** authorized group
    results and rankings recalculate while per-track scores and progress remain unchanged.

---

### User Story 4 - See Updates Without Reloading and Recover Connectivity (Priority: P1)

As a participant, I want confirmed shares, track feedback, manual collection ratings,
favorite/least-liked choices, group scores and rankings to stay current automatically,
while connection recovery, conflicts and permission loss remain understandable.

**Why this priority**: Maintains a reliable shared view. Addresses principles IV and V.

**Independent Test**: Use independent sender/reviewer/current-group contexts and an outsider. Alternate
confirmed collection/rating/choice changes, connection loss, event retries, simultaneous
edits and membership removal, checking private results and ranking updates as well as feed state.

**Acceptance Scenarios**:

1. **Given** the authorized test sessions are connected and the service is available within its quotas,
   **When** a collection, rating, or comment is confirmed in one session, **Then** every other session
   permitted to view that change displays it within five seconds without manual reloading.
2. **Given** connection loss, **When** the person attempts to save a rating,
   **Then** the application indicates unavailability and does not report unconfirmed success.
3. **Given** a session that was disconnected, **When** connectivity returns,
   **Then** it retrieves current information before indicating synchronization is complete.
4. **Given** a retry of the same operation or an old update received after a new one,
   **When** state is updated, **Then** there are no duplicate records, double-counted ratings,
   or silent regression of the average and progress.
5. **Given** a direct share with two reviewers, **When** one reviewer confirms feedback,
   **Then** its authorized sender and reviewer sessions receive the update within five
   seconds; the other recipient receives no private review data or revealing summary.
6. **Given** a group share and authorized group member sessions, **When** a review is
   confirmed, **Then** those members receive its update within five seconds while
   sessions outside that group share receive no private review information.
7. **Given** a removed member with an open group view, **When** removal is confirmed,
   **Then** new server requests and private group updates are denied, and the connected
   interface stops displaying that group's private content within five seconds under
   the documented network and service conditions.
8. **Given** two sessions editing the same reviewer's track occurrence from the same saved
   state, **When** one edit is confirmed before the other is submitted, **Then** the stale
   edit is rejected with a visible conflict message and the latest confirmed values.
   The first edit is preserved until the reviewer explicitly resubmits from the current
   state; retries do not silently overwrite it or count a second rating.

9. **Given** current group members viewing results and rankings, **When** an eligible
   reviewer confirms a changed manual collection rating, **Then** the current aggregate
   and affected ranking positions converge within five seconds under the documented
   service/network conditions without altering track means.
10. **Given** a member with an open ranking or collective-results view, **When** removal
    is confirmed, **Then** that private view is denied and cleared under the same
    five-second conditions as other group content.

---

### User Story 5 - Comment on Tracks and Record Highlights (Priority: P2)

As a reviewer, I want optional plain-text impressions alongside occurrence ratings,
for playlists and albums, with the same authorship, edit and private audience rules.

**Why this priority**: Adds context to scores without creating a separate conversation.
Addresses principles I, III, IV, and IX.

**Independent Test**: Save a score with and without a comment on prepared playlist/album occurrences; verify
reviewer attribution, inert malicious text, direct/group visibility and comment-only edits.

**Acceptance Scenarios**:

1. **Given** a rateable track, **When** the recipient saves a score with a comment,
   **Then** the comment is associated with that track and that participant's rating.
2. **Given** a valid score, **When** it is submitted without a comment,
   **Then** the rating is accepted; a comment is not mandatory.
3. **Given** a saved comment, **When** an authorized viewer views the track,
   **Then** they can identify the comment and its reviewer.
4. **Given** a highlight about a track, **When** the reviewer describes it in the comment,
   **Then** the text is preserved as an observation without requiring tag classification.
5. **Given** a comment containing an attempt to execute active content,
   **When** participants view it, **Then** it is treated as text and does not execute
   actions, change the session, or expose private information.
6. **Given** a saved comment, **When** visibility is checked, **Then** a direct-share comment
   is visible only to its sender and reviewer, and a group-share comment is visible to
   authorized members of that group.
7. **Given** an authorized reviewer with a saved comment on a completed review, **When**
   they edit that comment and save, **Then** authorized viewers see the latest confirmed
   text with the same authorship and sharing permissions; scores, average, and progress
   are unchanged by the comment-only edit.

---

### User Story 6 - Revisit Collection and Review History (Priority: P2)

As a participant, I want to revisit authorized playlist and album shares, their frozen
evaluation scope, track feedback, manual general ratings and choices across sessions.
Current members can revisit their group's collective results without exposing direct reviews.

**Why this priority**: Preserves the value of ratings after submission.
Addresses principles V and VII.

**Independent Test**: Prepare ten playlist/album submissions with partial/complete tracks, manual ratings,
choices and edits. Reopen genuine sessions and compare known data, including pre-join
group history, removed-member denial, direct grants and disclosed capped imports.

**Acceptance Scenarios**:

1. **Given** collections submitted at different times, **When** a participant views history,
   **Then** they find the submissions with authorship and their respective ratings.
2. **Given** a partially rated collection, **When** it is viewed in another session,
   **Then** confirmed scores, comments, the partial average, and progress are preserved.
3. **Given** a completely rated collection, **When** it is reopened later,
   **Then** it shows the imported list, per-track scores, and the corresponding completed average.
4. **Given** a space with no submissions, **When** the feed or history is opened,
   **Then** it presents an understandable empty state and the option to share a collection.
5. **Given** group history created before a participant joined, **When** that participant
   becomes a current member, **Then** the group history is accessible without a join-date cutoff.
6. **Given** a participant removed from a group, **When** they try to reopen its history
   through a feed, history view, or saved link, **Then** private group content is denied.
7. **Given** a confirmed rating or comment edit, **When** an authorized participant returns
   in a new session, **Then** history shows the latest confirmed values and corresponding
   average and progress; a prior-version audit view is not required.

8. **Given** a capped alternative import, **When** it is reopened later, **Then** the
   frozen evaluation set and cutoff disclosure remain associated with its history;
   later source changes do not silently expand its denominator.
9. **Given** a confirmed manual score or favorite/least-liked edit, **When** an authorized
   viewer returns, **Then** current history and collective results reflect the same
   confirmed feedback without a prior-version audit requirement.

---

### User Story 7 - Use and Install on Mobile (Priority: P2)

As a participant, I want responsive dark-only mobile access to invitations, playlist
and album sharing, individual/general reviews, group results and the two ranking views,
and to reopen the same private space from my iOS or Android home screen.

**Why this priority**: Supports the primary usage context. Addresses principles III and IX.

**Independent Test**: Complete access, both collection types, scoring, choices, collective results and ranking
lookup on each platform; install/reopen and verify 320 CSS px, 200% text, keyboard, touch
and screen-reader usability without enforcing the prototype's fixed dimensions.

**Acceptance Scenarios**:

1. **Given** a compatible iOS or Android phone, **When** the person installs the application
   through the available flow, **Then** they can open it from the home screen.
2. **Given** the application was opened from its icon, **When** the person accesses their
   space, **Then** the same permissions and shared information are respected.
3. **Given** a screen 320 px wide or text enlarged to 200%, **When** the person completes
   the primary flows, **Then** essential content and actions remain usable.
4. **Given** touch, keyboard, or screen reader navigation, **When** the person selects
   a score and views comments, **Then** they can identify controls, values, and messages.

5. **Given** fewer than three ranked entries and a narrow screen, **When** the ranking
   is opened, **Then** all three podium positions remain understandable, available
   numeric grades/star descriptions are accessible, and empty slots are not shown as zero.

---

### User Story 8 - Read Collective Group Results (Priority: P2)

As a current group member, I want to compare the individual feedback on each shared
playlist or album and see the group's current final score, so that the collective
assessment is understandable without mixing private direct reviews.

**Why this priority**: Adds the requested collective result while retaining established
private sharing and independent reviewer authorship.

**Independent Test**: Prepare a group share, reviewers with manual ratings 5 and 2, a
member without a general rating, and an outsider. Read individual/general/track data,
then change one manual score and verify the current group result and access boundaries.

**Acceptance Scenarios**:

1. **Given** current group membership, **When** a playlist or album's results are opened,
   **Then** permitted individual scores, track means, general ratings and choices are
   attributable to their reviewers and distinct from the group final score.
2. **Given** manual general ratings 5 and 2 and one person with no general rating,
   **When** the group score is shown, **Then** it is 3.5 from two contributions, with
   the missing rating excluded rather than treated as zero.
3. **Given** the same two ratings and a third recorded manual rating of 0, **When**
   results update, **Then** the group mean is 2.33 from three contributions.
4. **Given** reviewers with different numbers of track ratings, **When** their manual
   general ratings contribute, **Then** each recorded general rating has equal weight;
   no weighting by track count or silent combination with the track mean occurs.
5. **Given** no recorded manual general ratings, **When** results are opened, **Then**
   no numeric group mean is fabricated and the no-general-ratings state is visible.
6. **Given** an authorized manual-rating edit, **When** it commits, **Then** collective
   results use current confirmed values and show the number of contributing ratings.
7. **Given** a separate direct share of the same Spotify collection, **When** group
   results are read, **Then** no direct-review data or revealing aggregate is included.
8. **Given** an outsider, pending invitee, removed member or administrator without
   current membership, **When** they request group results, **Then** no private data is disclosed.
9. **Given** a group that contains both albums and playlists, **When** a member opens
   one collection's results, **Then** only that context-specific submission's feedback
   contributes; other collections are not collapsed into its grade.

---

### User Story 9 - Compare Separate Group Rankings (Priority: P2)

As a current group member, I want one ranking of the group's playlists and another of
its albums, ordered by their collective final scores, with a visible top three and
remaining places below.

**Why this priority**: Makes the requested group comparisons useful without combining
collection types or introducing a public/personal ranking scope.

**Independent Test**: Prepare one group with known playlist and album general ratings,
including zero, unrated entries and at least four eligible entries of one type. Open
both rankings as a current member, an outsider and a removed member; edit a manual
rating and verify the new order and podium states.

**Acceptance Scenarios**:

1. **Given** a group with both types, **When** its rankings are opened, **Then** playlists
   and albums are ranked separately using that group's collective final scores.
2. **Given** four eligible entries with distinct means, **When** a ranking is viewed,
   **Then** the three highest occupy the highlighted first/second/third slots and the
   fourth appears below in a smaller, readable row with its position.
3. **Given** one eligible entry, **When** the ranking is opened, **Then** it occupies
   first place and second/third remain visible as empty positions.
4. **Given** two eligible entries, **When** it is opened, **Then** first/second are
   populated and the third position remains visible and empty.
5. **Given** no eligible entries of a type, **When** that ranking is opened, **Then**
   all three positions remain visible and empty without fabricated winners or zero grades.
6. **Given** a ranked entry, **When** it is displayed, **Then** its collection identity,
   numeric group grade, star representation and position are understandable together.
7. **Given** a recorded general score of zero and an otherwise unrated collection,
   **When** eligibility is checked, **Then** the zero-rated entry is eligible and the
   entry without a group mean does not receive a fake zero or a podium place.
8. **Given** a confirmed general-rating edit changing collective order, **When** it
   reaches authorized views, **Then** grades, star values and positions correspond to
   the same persisted state within the existing real-time conditions.
9. **Given** a different group or direct sharing context, **When** this group's ranking
   is computed, **Then** feedback from those other contexts contributes nothing.
10. **Given** an outsider or removed member with an old ranking link, **When** they
    request it, **Then** membership authorization prevents disclosure.
11. **Given** a source collection imported through the capped path, **When** it appears
    in results or a ranking, **Then** its scope limitation remains discoverable rather
    than implying its evaluation covered the whole original.

### Edge Cases

- **Incorrect link or unsupported content:** accept eligible Spotify playlist and album
  links; explain unsupported track, artist, show or profile links without false publication.
- **Connected playlist ownership/collaboration:** the connected path must explain missing
  permission. A catalog album is not user-owned; non-linked public-source eligibility is
  separate and never claims verified ownership from a URL alone.
- **Denied, expired, or revoked Spotify authorization:** explain the condition and permitted
  next step without exposing credentials or treating import as completed.
- **VIP account outside provider allowance:** application VIP designation cannot authorize
  connected Spotify access. Explain the connected-path failure without denying the user's
  ordinary features or silently substituting another person's grant.
- **Quota, unavailability, or interrupted import:** indicate failure or progress and prevent
  an incomplete list from being treated as the final list for evaluation.
- **Existing personal Premium unavailable:** explain the affected integration's unavailability
  and degrade gracefully; do not buy, renew, or upgrade a subscription with project funds.
- **Empty collection or no rateable occurrences:** reject submission with an explanation,
  without calculating an average or presenting division by zero as a score.
- **Non-importable track, local item, episode, or unidentified item:** exclude it from evaluation
  and report the items and available reasons, without automatic scores or silent exclusion.
  Playback unavailability alone does not establish that metadata cannot be imported;
  the criterion is whether the track can be identified and imported.
- **Repeated track:** each occurrence receives its own rating and participates separately
  in the average and progress, even when the musical content is the same.
- **Collection changed during import:** do not announce completion of a list that mixes
  incompatible compositions; import must produce a consistent reference list.
- **No rating versus zero score:** display distinct states; a confirmed zero participates
  in the average, while an unrated track does not implicitly receive zero.
- **Invalid score:** prevent values outside the scale; do not silently convert invalid
  values into valid scores.
- **Edits after completion:** an authorized reviewer can change their own saved scores
  and comments without reopening or duplicating the review. Replacing a valid score
  changes the average, not the number of rated occurrences; comment-only edits change neither.
- **Invalid or unauthorized edit:** preserve the last confirmed rating, comment, average,
  and progress; previous authorship does not bypass lost group access.
- **Multiple sessions for the same reviewer:** reject a stale edit of the same occurrence
  after another edit is confirmed, display the conflict and latest confirmed values, and
  require explicit resubmission from current state. Do not silently overwrite confirmed
  feedback or count a rating twice in the average.
- **Submission retry after network failure:** do not create two records by retrying
  the same operation. Intentionally sharing the same link again is a separate decision.
- **Subsequent external changes:** additions, removals, and reordering on Spotify do not
  change the track list or recalculate ratings for an already imported submission.
- **External link unavailable after submission:** distinguish failure to open Spotify
  from failure to access Mayonnaise rating history; respect the provider's conditions.
- **Long text or malicious content:** maintain readability and privacy and do not execute
  comment text as instructions or active content; length limits have not been set.
- **Logout on a shared device:** prevent the next session from seeing the previous
  participant's private data, including when reopening the installed application.
- **Followed participant not selected for a share:** following alone does not grant access
  to that collection's private submission, ratings, comments, or updates.
- **Someone following a recipient:** follow relationships do not transitively grant access.
- **Unfollowing an existing direct recipient:** remove them from eligibility for new direct
  shares until followed again, while preserving existing direct-share access and reviews.
  The reverse follow and private group memberships are independent.
- **Multiple direct recipients:** each can access the collection and their own feedback;
  another recipient's scores, comments, average, and progress remain private.
- **Private group share:** authorized group members can read all reviews for that group's
  share; membership does not authorize impersonating another reviewer.
- **Same Spotify collection in different contexts:** sharing it with a group does not expose
  reviews previously submitted through a separate direct share.
- **Pending group invitation:** membership and private group access are not granted before
  the intended account explicitly accepts.
- **Group posting without current membership:** pending invitees, removed members, and
  other non-members cannot post collections to the group; previous authorship and application
  ownership do not replace current membership.
- **Unauthorized group administration:** ordinary members and an administrator who is
  not that group's creator cannot invite or remove members.
- **Wrong-account acceptance:** possession of a group invitation reference does not permit
  a different account to accept it.
- **Repeated group-invitation acceptance:** do not create duplicate memberships.
- **Group membership changes:** accepted members gain access to the complete group history;
  removal ends access to both earlier and future group content.
- **Removed reviewer:** authorship of a group review does not retain access after removal;
  the group's history remains available to current members.
- **Independent sharing contexts:** group membership changes neither expose private direct
  reviews to the group nor revoke access granted by an independent direct share.
- **Forwarded private link:** knowing the link does not replace account and audience checks.
- **Spotify access versus Mayonnaise privacy:** private sharing does not change the
  original Spotify collection's visibility or grant Spotify collaboration permissions.
- **Forwarded account-activation code or unverified email:** a usable code alone grants no
  activation. The signed-in account must have the verified email specified by the administrator
  for that invitation; rejection preserves prior activation and code-consumption state.
- **Invitation preview or repeated page load:** opening, previewing, or refreshing an
  invitation does not redeem it; activation requires an explicit action after sign-in.
- **Expired, invalid, or consumed invitation:** deny activation without granting private
  access. Account-activation invitations expire 24 elapsed hours after issuance and are
  invalid at or after that deadline; opening the link does not extend their lifetime.
- **Failed sign-in or interrupted activation:** a failed login preserves active/inactive
  membership and consumed/unused invitations. Failed activation does not consume a code,
  grant membership or deactivate an already active account. Retrying must not create
  duplicate activations. Already active accounts cannot redeem another activation code.
- **Concurrent invitation redemption:** account activation and code consumption form one
  indivisible operation; at most one account can redeem the same invitation.
- **Repeated sign-in after activation:** use normal authentication without asking for a new code.
- **Attempted invitation creation by a non-administrator:** deny issuance and protect invitation secrets.
- **Activation-code guessing:** enforce a finite attempt limit and retry window defined
  during planning, without exposing codes in logs or consuming them on rejected attempts.

- **Unknown or capped source scope:** show imported/rateable counts and known cutoff;
  never invent the original total or say the whole original was imported. A parser/source
  failure within the promised scope remains a failed/incomplete import.
- **Missing artwork or metadata:** use a consistent placeholder/unknown value, preserving
  source attribution and authenticated submitter identity.
- **Administrator versus group creator:** invitation administration does not expose group
  history or authorize membership changes in groups the administrator does not create.
- **Normal/VIP equality:** identical private data and review weights are available under
  the same audience rules; VIP is not billing, a better vote or broader group visibility.
- **Manual zero versus no general score:** zero contributes to collective means/rankings;
  absence does not. Track means do not substitute for missing manual votes.
- **Favorites, repeated tracks and short collections:** choices refer to frozen occurrence
  identity, not text matching; optionality and one-occurrence handling follow the explicitly
  stated working defaults, not a fabricated extra song.
- **Foreign or stale general feedback:** validate reviewer, share, occurrence, current
  membership and expected state before disclosure/write, as for track feedback.
- **No collective scores:** show the no-general-ratings state and three empty positions
  for the relevant type; do not infer zero grades or create fake entries.
- **One/two ranked entries:** keep all three highlighted positions visible. Real entries
  fill the highest available slots and remaining slots are empty.
- **Equal collective means:** maintain a stable order without modifying scores or adding
  highlighted positions; compare the confirmed values consistently before display rounding.
- **Membership removal and retained feedback:** revoke the removed viewer's result/ranking
  access; do not erase group feedback just to change a ranking. Retained-review aggregation
  follows the disclosed working default until separately revised.
- **Invitation email failure or delay:** do not claim delivery or activation, expose the
  code to other users, or extend its issuance-based deadline silently.
- **Normal playlist submission:** deny it consistently in the MVP; Normal participants
  retain album imports and authorized received-playlist reviews. A visible playlist link,
  administrator role or another participant's connection does not grant import eligibility.

## Requirements *(mandatory)*

### Functional Requirements

- **FR-001**: The system MUST restrict private access to authenticated participants with activated accounts, recognizable identities and no fixed two-participant business cap. All accounts require private invitation activation except the restricted initial administrator in FR-064. Administrative role and Normal/VIP import eligibility are separate from private audience authorization.
- **FR-002**: The system MUST prevent reading, writing, and following private updates by
  anonymous people, inactive accounts, expired sessions, or unauthorized people.
- **FR-003**: The system MUST attribute submissions, ratings, and comments to the authenticated
  identity; one participant cannot impersonate the other.
- **FR-004**: The system MUST allow sessions to be ended and prevent exposure of private
  data after session termination or expiration.
- **FR-005**: The system MUST accept eligible Spotify album links from activated Normal and VIP participants without a personal Spotify connection. Playlist submission MUST require VIP eligibility and that submitter's own authorized Spotify owner/collaborator connection. Normal participants MUST NOT submit playlists in this MVP, while retaining access to authorized received-playlist reviews.
- **FR-006**: Import MUST establish the ordered eligible occurrence set for its declared scope. Connected playlist imports MUST consider all required pages. The non-linked album path imports at most the first 100 source occurrences. The interface MUST distinguish an actual cutoff from a complete original and reject or retain incomplete work when the promised scope cannot be established.
- **FR-007**: The system MUST distinguish collecting, failed and confirmed publication states. Capped publication MUST preserve its cutoff disclosure; incomplete work MUST NOT be announced as a successful full import.
- **FR-008**: The system MUST explain invalid links, insufficient permissions, and external
  service failures understandably, allowing another attempt when applicable.
- **FR-009**: A participant's feed MUST contain only direct shares they sent/received or shares in current private groups, showing collection type/identity, available cover, source link, submitter, time and authorized evaluation state. Labels MUST distinguish track means, manual general ratings and collective scores; authorship or administrator status alone grants no group access.
- **FR-010**: The system MUST allow opening the collection in the external service;
  the application does not play or download track audio.
- **FR-011**: The ordered occurrence set captured for the declared import scope MUST remain frozen against later Spotify composition changes, for playlists and albums. A capped import does not silently expand to the original's later/full list.
- **FR-012**: The system MUST allow selected direct recipients and eligible members of a
  group receiving a collection to rate its track occurrences. The sender and participants
  outside the authorized sharing context MUST NOT rate it.
- **FR-013**: Each score MUST belong to the set 0, 0.5, 1, 1.5, 2, 2.5, 3, 3.5, 4, 4.5, 5.
  Values outside this set cannot be saved or silently converted.
- **FR-014**: The system MUST distinguish unrated occurrences from occurrences rated zero;
  both situations must be recognizable in the interface.
- **FR-015**: Each reviewer's track-based mean MUST be the equal-weight arithmetic mean of their confirmed occurrence scores. Unrated occurrences count neither as zero nor in its divisor. This derived value MUST remain distinct from the manual general collection rating.
- **FR-016**: The track mean MUST appear from the first confirmed occurrence rating, identified as partial until all imported rateable occurrences are scored, with that reviewer's rated/total progress.
- **FR-017**: Derived track and group means MUST use original contributing scores, no intermediate rounding, and display up to two decimal places rounded to the nearest hundredth with upward ties; 0.125 becomes 0.13. A derived mean need not fall on a half-step.
- **FR-018**: Track evaluation is complete when that reviewer has scored all rateable occurrences in the frozen imported scope. With no track scores, show no track mean. Manual general ratings or favorite/least-liked choices MUST NOT fabricate or change track progress.
- **FR-019**: The system MUST allow an optional text comment alongside an occurrence's score,
  written by its reviewer. Direct-share comments are visible only to that reviewer and
  the sender; group-share comments are visible to authorized members of the target group.
- **FR-020**: Track highlights MUST be recorded in these free-text comments; there is
  no requirement for tags, overall collection comments, or participant conversations.
- **FR-021**: Confirmed shares, track feedback, manual general ratings, favorite/least-liked choices, derived group scores, rankings and membership changes MUST converge in affected authorized foreground views within five seconds under the documented active-connection, service-availability and quota conditions.
- **FR-022**: Every displayed score, choice, average, progress value and rank MUST correspond to confirmed persisted feedback. No unconfirmed write may appear successful or silently contribute to a group grade.
- **FR-023**: The system MUST indicate connection loss and retrieve current state after
  reconnection before declaring synchronization complete.
- **FR-024**: Retries and duplicate/out-of-order responses MUST NOT duplicate submissions/reviews/choices, double-count scores or regress confirmed values/ranks. A stale edit of the same feedback MUST be rejected with a visible conflict and current authorized values, requiring explicit resubmission rather than silently losing an edit.
- **FR-025**: Authorized history MUST preserve context-specific playlist/album submissions, frozen import scope and coverage, authorship, occurrence feedback, track means/progress, manual general ratings and choices across sessions, subject to provider retention and current permission.
- **FR-026**: The system MUST distinguish no shares, no track ratings, no manual general ratings and insufficient ranked entries through understandable empty states. Empty ranking positions MUST remain visible without fictional grades.
- **FR-027**: The system MUST support adding the application to the home screen on iOS and
  Android and apply the same access rules when opened this way.
- **FR-028**: The system MUST offer usable navigation and rating on small screens,
  with accessible control names, identifiable focus, and feedback that does not rely only on color.
- **FR-029**: The system MUST treat comments and links as untrusted input, without executing
  received content or exposing participant and integration credentials.
- **FR-030**: Displayed information obtained from Spotify MUST identify its source and offer
  access to the corresponding content according to the provider's applicable conditions.
- **FR-031**: The system MUST exclude episodes, local items, and items that cannot be identified
  or imported as tracks from evaluation, report their exclusion and available reasons,
  and exclude them from the average's divisor and the rateable total.
- **FR-032**: The system MUST reject sharing a collection with no rateable track occurrences
  and explain why, without creating a completed submission in the feed.
- **FR-033**: Each imported occurrence MUST retain its identity within the preserved list;
  repeated tracks receive separate scores and comments and count separately in the average.
  Retrying the same operation cannot create a new artificial occurrence.
- **FR-034**: Progress MUST be calculated separately for each reviewer as their confirmed
  occurrence ratings over the preserved list's rateable total. Equality with a total
  greater than zero marks only that reviewer's evaluation complete.
- **FR-035**: Except for restricted initial administrator provisioning in FR-064, account
  activation MUST require a valid, unused, unexpired private invitation code. Activation
  and code consumption MUST succeed or fail together. One code MUST activate at most one
  account, including under concurrent attempts. Denied activation MUST preserve the prior
  account and invitation state: an inactive account stays inactive, an unused code stays
  unused, and a consumed code stays consumed. Missing, invalid, expired and consumed codes
  MUST be rejected; attempting activation on an already active account MUST NOT consume
  another code or deactivate the account.
- **FR-036**: Application membership MUST NOT be capped at two or equated with Spotify's external account allowance. Connected VIP playlist imports remain within provider allowance. Normal participants retain non-linked album imports and authorized shared-playlist participation; this does not grant personal Spotify connection or playlist-submission rights.
- **FR-037**: Only activated administrators MUST be authorized to enter a recipient email and issue/send a private account-activation invitation. Common users, regardless of VIP/Normal subtype, cannot issue account invitations. This authority is separate from group-creator membership administration.
- **FR-038**: An administrator-issued invitation MUST contain an unguessable code bound to the intended recipient email and be delivered privately to that address. The application MUST distinguish invitation creation, sending outcome and account activation; sending alone grants no membership.
- **FR-039**: Account-invitation recipients MUST authenticate, have a verified account email
  matching the invitation's intended recipient email, and explicitly request activation
  before redemption. Different-account or unverified-email attempts MUST NOT activate an
  account or consume the invitation. Opening, previewing, or refreshing its link MUST NOT consume it.
- **FR-040**: After activation, subsequent sign-ins MUST use ordinary authentication without
  requiring a new account-activation invitation or using the activation code as a reusable password.
  Failed authentication MUST preserve activation and invitation state, including for
  the initial administrator; authentication throttling does not deactivate membership.
- **FR-041**: Account-activation invitations MUST expire 24 elapsed hours after issuance.
  Activation at or after that deadline MUST be rejected without activating an account or
  consuming the code. Opening or previewing an invitation MUST NOT extend its lifetime.
- **FR-042**: Activation attempts MUST be subject to a finite, enforced attempt limit and
  restricted retry period. Their values MUST be documented during planning; denied attempts
  MUST NOT activate accounts or consume a still-valid invitation.
- **FR-043**: Application-persisted invitation verification data MUST NOT disclose the original usable code. Raw codes MUST NOT enter application logs, public views or other participants' data. Delivery to the intended recipient and any authorized one-time administrative display must preserve confidentiality.
- **FR-044**: An activated participant MUST be able to follow and unfollow another activated
  participant. Following is directional: A following B adds B to A's sharing list without
  requiring B to follow A. A repeated follow MUST NOT create duplicate entries. Unfollowing
  MUST remove B from eligibility for A's new direct shares until A follows B again, without
  changing existing direct-share permissions or reviews, B's reverse follow, or group memberships.
- **FR-045**: A sender MUST choose a sharing context: a direct share to one or more selected
  people they follow, or a share with a previously created private group they are authorized
  to use. Neither mode makes the collection public to other application members.
- **FR-046**: A direct collection share MUST be accessible only to its sender and selected
  recipients; each review within it is restricted to its sender-reviewer pair.
  A group share MUST restrict collection and review access to its authorized group audience.
- **FR-047**: Account activation, following, being followed, a connection with a recipient,
  or possession of a forwarded link MUST NOT grant access by themselves. Access requires
  an applicable direct-share permission or authorization within the target private group.
- **FR-048**: Feeds, history, direct access, and live updates MUST enforce the sharing-mode
  permissions for collection data and for each review, including comments and derived summaries.
- **FR-049**: When creating a direct share, the system MUST reject a new direct recipient
  the sender does not follow, without silently expanding the authorized audience.
  Group recipient eligibility is governed by the target group's membership rules.
- **FR-050**: Activated participants MUST be able to find other activated participants by
  their unique @username handle through an in-app search menu and follow the selected
  account from the results. Handles MUST contain only ASCII letters/digits and be stored
  in lowercase; an optional leading @ is an input/presentation prefix, not part of the
  stored identifier. Signup/setup, bootstrap and search MUST apply the identity policy
  below. The selected account UUID MUST determine the resulting follow.
- **FR-051**: Member search and following lists MUST expose only the participant identification
  needed for discovery and selection; they MUST NOT expose private collections, ratings,
  comments, account activation codes, or credentials.
- **FR-052**: In a direct share, only the sender and an individual reviewer MUST be able to
  read that reviewer's scores, comments, average, and progress. Selecting multiple people
  for the same collection MUST NOT disclose their reviews to one another.
- **FR-053**: Activated participants MUST be able to create private groups. Every current
  group member MUST be allowed to share an eligible collection with that previously created
  group, including members who are not its creator. Pending invitees, removed members, and
  other non-members MUST NOT post to it. The group MUST retain its creator identity,
  separately from the application's invitation-issuing administrator role.
- **FR-054**: Current group members MUST be able to read the group's complete playlist/album and review history, including earlier track feedback, manual ratings, choices, collective results and rankings. Nonmembers MUST receive none of that private content.
- **FR-055**: Only the group creator MUST be authorized to invite/remove members. Common membership and administrator status alone confer neither group administration nor private group access or posting rights.
- **FR-056**: Reviews MUST be scoped to their sharing context. Sharing the same Spotify
  collection directly and with a group MUST NOT copy or expose private direct-share feedback
  in the group context.
- **FR-057**: Each reviewer's scores and comments MUST remain associated with that reviewer.
  Permission to read another group member's review MUST NOT permit writing on their behalf.
- **FR-058**: A group creator MUST invite activated participants they follow. Each group
  invitation MUST identify its target account, and only that authenticated account may
  accept it to become a member; invitation issuance alone MUST NOT add the member.
- **FR-059**: A pending group invitation MUST NOT grant access to group collections or reviews.
  Group invitations MUST remain separate from account-activation invitations and MUST NOT
  activate an account or require consuming another account-activation code.
- **FR-060**: Successful acceptance MUST establish one membership for the invited account
  in the target group. Repeated or concurrent acceptance MUST NOT create duplicate
  memberships or admit another account.
- **FR-061**: Confirmed removal MUST end that participant's access to all private content
  in the group, including past shares and reviews they authored. Subsequent server reads
  and private group updates MUST be denied; connected views MUST hide the group's private
  content within five seconds under the documented service and network conditions.
- **FR-062**: Group membership changes MUST NOT change independent direct-share permissions
  or expose direct reviews within the group. Removing a member MUST NOT automatically
  delete historical group reviews that remain available to current group members.
- **FR-063**: Eligible reviewers MUST be able to edit their own saved occurrence scores/comments, manual collection rating and favorite/least-liked choices while authorized, including after track completion. Track-score changes recalculate only the relevant track mean/progress semantics; manual-score changes recalculate group means/ranks without changing track counts; comment/choice-only edits do not alter numeric grades. Invalid, unauthorized or stale edits preserve confirmed state.
- **FR-064**: The application MUST distinguish administrator and common-user profiles. A trusted infrastructure operator MUST provision only the initial administrator against an explicitly selected, verified Auth identity without a Mayonnaise invitation, before any ordinary account activation. This operation MUST be auditable and idempotent for the same identity, remain closed to other identities after bootstrap, and expose no public setup/self-claim flow. All subsequent accounts MUST activate by invitation before any trusted role promotion. Administrative invitation authority MUST NOT override direct-review privacy, group membership or review authorship rules.
- **FR-065**: Common users MUST have Normal or VIP import eligibility. Both subtypes MUST have the same authorized review, group, comment, result, ranking and mobile capabilities. Both may import/share albums; only VIP may connect a personal Spotify account and submit eligible owned/collaborative playlists. An existing Premium subscription, application VIP designation and provider allowlisting are separate facts.
- **FR-066**: Roles and import eligibility MUST come from trusted application authority. A common user's signup/form payload or editable identity metadata MUST NOT promote that user to administrator or VIP.
- **FR-067**: The non-linked catalog path MUST accept eligible Spotify album links without requiring a participant's personal Spotify connection, preserve original occurrence order and duplicate occurrences, and fail safely when the source is unavailable or insufficient. It MUST NOT import playlists or reuse another participant's personal grant.
- **FR-068**: The non-linked album path MUST import the first 100 source occurrences when the original exceeds that scope, identify the cutoff prominently and preserve it in detail/history/results. Only eligible occurrences inside the captured scope enter rating totals. Known excluded or omitted counts MUST be accurate; an unknown original total MUST not be invented.
- **FR-069**: The system MUST display a source-provided collection title and available cover/artist metadata, with durations and other optional details only when obtained. Missing data MUST use clear placeholders; source curator/artist identity MUST NOT replace authenticated Mayonnaise submitter identity.
- **FR-070**: Album imports MUST use the same context-specific sharing, frozen-order, occurrence-scoring, manual-rating, choice, comment, history, collective-result and ranking behavior as playlist imports. A catalog album MUST NOT require playlist ownership/collaboration.
- **FR-071**: Each eligible reviewer MUST be able to assign one current manual overall collection rating independently of individual track scores, on the same 0–5 half-step scale. An unset manual rating is distinct from zero.
- **FR-072**: The interface MUST label the reviewer's track mean, their manual general rating and the group collective score separately. No component may silently replace or combine these values.
- **FR-073**: Each reviewer MUST be able to choose one favorite occurrence from the collection's imported eligible set.
- **FR-074**: Each reviewer MUST be able to choose one least-liked occurrence from that same set.
- **FR-075**: Favorite/least-liked choices MUST belong to the current authorized review and imported collection, preserve reviewer attribution, follow the same direct/group visibility and edit-conflict rules, and never change scores or progress by themselves.
- **FR-076**: For a group-shared collection, current members MUST have a collective-results view containing the authorized individual reviews and a separately identified current group final score.
- **FR-077**: The group final score MUST be the equal-weight arithmetic mean of recorded manual general collection ratings for that group share. Missing manual ratings MUST be excluded; zero MUST count. Track means or track counts MUST NOT silently affect its weights, and no numeric mean may be shown when no general ratings exist.
- **FR-078**: Collective results MUST identify their contributing general-rating count and update from current confirmed values. The term final score MUST NOT imply a locked grade, a reveal gate or that every eligible member has already reviewed.
- **FR-079**: Each private group MUST have two separate rankings: one for playlists and one for albums. These are group rankings based on the corresponding collective final scores, not personal or public rankings.
- **FR-080**: Ranked entries MUST be ordered from the highest group final score downward within their type, using only that group's context-specific feedback. Another group's ratings and private direct reviews MUST contribute nothing.
- **FR-081**: Each ranking MUST always display first, second and third highlighted positions, even when fewer eligible entries exist.
- **FR-082**: A populated highlighted position MUST identify the collection, its ranking place, numeric final grade and a star representation of that same grade, with accessible textual identification.
- **FR-083**: Ranked entries after third place MUST appear below the highlighted three in smaller but readable rows, preserving descending order and their positions.
- **FR-084**: Missing top-three entries MUST remain visible empty placeholders, not fake collections, zero-rated entries or invisible removed positions. A real zero-rated collection is not missing.
- **FR-085**: A private group MUST support multiple playlist and album shares. Neither a previous share nor a group rank makes that group a single-collection container.
- **FR-086**: Manual general feedback, choices, collective results and rankings MUST respect existing server-authorized audiences, live-session checks, membership revocation, persistence and retry/conflict rules; administrator or VIP status does not bypass them.
- **FR-087**: Invitation-delivery failure MUST produce an understandable sending failure, not false delivery or activation. Retrying MUST NOT consume a code, duplicate accounts or silently extend the 24-hour issuance-based deadline.
- **FR-088**: Non-linked album import MUST use a usable source within provider and zero-cost constraints and MUST NOT add in-application playback, borrowed personal credentials, paid extraction, scraping or a claimed capacity bypass. A broader Normal playlist-import path requires an explicit future scope and source decision.

### Identity Input and Comparison Policy

This policy applies to signup/sign-in email input, initial username setup, invitation
entry, bootstrap and member search. Emails remain private and are never member-search keys.
The database's canonical identity policy is detailed in
[data-model.md](data-model.md#identity-normalization-and-comparison).

**Usernames:** use unique handles displayed as `@alice123`. The identifier contains one or
more ASCII letters/digits only: input `A–Z`, `a–z`, `0–9`; canonical stored values `a–z`,
`0–9`. Signup/setup, bootstrap and search accept the bare handle or exactly one leading
ASCII `@`. Remove that prefix and lowercase ASCII letters; store no `@`. Spaces (including
boundary spaces), underscores, dots, hyphens, accents, emoji, control characters and other
Unicode/special characters are rejected, never silently removed or transliterated.
Do not use NFKC/NFC to turn unsupported characters into an accepted handle. Empty handles
are invalid for registration/setup; an empty search or a lone `@` returns no results.
Duplicate canonical handles are rejected atomically, so `Alice123`, `alice123` and
`@ALICE123` identify the same handle. Every application identity label renders exactly
one `@` plus the canonical lowercase value, using a shared presentation element. Search
uses literal canonical text, not SQL wildcards. Selection/authorization always uses UUID.
This does not add a separate display-name field or a username-change flow.

**Emails:** trim boundary Unicode whitespace, validate a single ASCII mailbox and lowercase
both local and domain parts, matching the selected Auth provider's case handling. The local
part permits ASCII letters/digits and `!#$%&'*+-/=?^_` plus the backtick, `{|}~` and dot;
dots cannot be leading, trailing or consecutive. Domain labels permit ASCII letters,
digits and interior hyphens, separated by dots; no empty labels or boundary hyphens.
The local part is at most 64 bytes, each domain label at most 63 bytes and the entire
address at most 255 bytes. No internal whitespace, quoted local parts, display names,
comments, address lists or Unicode mailbox characters are supported in this MVP. An
internationalized domain must already be in valid ASCII Punycode form; no automatic IDNA,
NFKC/NFC conversion, accent stripping, dot removal or `+tag` removal is performed.
Validation is not proof of ownership or delivery: activation compares the canonical
invitation email with the account's current trusted, verified Auth email.

| Context / input | Canonical result / expected behavior |
| --- | --- |
| Username setup/search: `Alice123`, `alice123`, `@ALICE123` | Same stored handle/key `alice123`, displayed `@alice123`; a second registration is rejected. |
| Username setup/search: `alice_123`, `alice.123`, `alice-123`, `alice 123` | Rejected; only ASCII letters/digits are permitted inside the handle. |
| Username setup/search: ` alice123 `, `Alice\u00A0Smith`, `alice123\n` | Rejected, including boundary whitespace and control characters; escapes denote actual characters. |
| Username setup/search: `José`, `Jose\u0301`, `Ａlice`, `alice🎵` | Rejected without accent stripping, Unicode normalization or symbol removal. |
| Username setup/search: `@@alice123`, `alice@123` | Rejected; at most one prefix `@` is accepted, only at the start. |
| Username setup: empty input or `@` | Rejected; the handle must be nonempty. |
| Username search: empty input or `@` | No results; no full-directory listing is returned. |
| Signup/invitation: `  Alice.Example+Music@EXAMPLE.COM  ` | `alice.example+music@example.com`; case/boundary-space variants match on redemption. |
| Email comparison: `alice+music@example.com` / `alice@example.com` | Different recipients; neither invitation activates the other identity. |
| Email comparison: `alice.example@example.com` / `aliceexample@example.com` | Different recipients; dots are preserved. |
| Email entry: `jose@example.com` / `josé@example.com` / `Ａlice@example.com` | First accepted; latter two rejected, never rewritten to an ASCII mailbox. |
| Email entry: `alice @example.com` / `Alice <alice@example.com>` | Rejected. |
| Email entry: `alice@xn--bcher-kva.example` / `alice@bücher.example` | ASCII Punycode form may pass validation; raw Unicode form is rejected. |

**Permission matrix:**

| Resource/action | Authorized actor and conditions |
| --- | --- |
| Provision initial administrator | Trusted infrastructure operator, explicit verified Auth UUID, initial installation only; no application invitation or public endpoint. |
| Issue/send account invitation | Activated administrator; specify the intended recipient email. |
| Redeem invitation | Signed-in account with matching trusted verified email, explicit activation and valid unused code before the 24-hour deadline. |
| Claim/change administrator or VIP authority from client input | Never permitted. Use trusted application authority, not participant-editable claims. |
| Connect personal Spotify account | VIP eligibility plus actual applicable provider permission; administration alone does not supply provider authorization. |
| Non-linked album import | Activated Normal or VIP participant with eligible catalog source; personal Spotify connection is not required. |
| Playlist import/submission | Activated VIP participant using their own authorized owner/collaborator connection; Normal attempts are denied. |
| Search/follow/unfollow | Activated participant, managing own directional follows; identity-only discovery. |
| Create direct share | Authorized importer selecting people currently followed at publication. |
| Read direct collection | Sender and selected recipients; unfollow does not revoke an existing grant. |
| Read direct review, manual score, choices or summaries | Sender and that individual reviewer only; no co-recipient disclosure or aggregate. |
| Create private group | Activated participant, recorded as creator. |
| Invite/remove group members | Only that group's creator; new invitee must be followed and activated. |
| Accept group invitation | Only its authenticated target; no account-activation code is consumed. |
| Share playlist/album into group | Any current member with an eligible import, excluding pending/removed/nonmembers. |
| Read group history/results/rankings | Current group members, including earlier history; administrator/previous author alone has no override. |
| Write/edit track/general feedback or choices | Current eligible reviewer, excluding the sender of that submission; no impersonation or editing another review. |
| Include feedback in group score/rank | Only manual general ratings of that group-specific share under the declared aggregation rules; no other-group/direct feedback. |
| Read history or receive live updates | Same current collection/review/group permissions as the corresponding reads. |

### Quality and Governance Constraints

- **QC-001**: The mobile experience MUST meet the constitution's criteria: a minimum width
  of 320 px without horizontal scrolling in critical flows, text enlarged to 200%,
  touch targets of at least 44 by 44 px, and its specified minimum contrast ratios.
- **QC-002**: Verification MUST cover access, isolation, complete import, scores, average
  calculation, comments, history, synchronization, account activation, administrator invitation
  sending, Normal/VIP isolation, connected-playlist and catalog-album imports, manual feedback,
  favorite/least-liked choices, group aggregation/rankings, membership changes and recovery.
- **QC-003**: MVP infrastructure and incremental project spending MUST remain zero.
  Constitution v4.0.0 permits only the application owner's already-held personal Spotify
  Premium subscription as an external prerequisite; it does not permit a project-funded
  subscription, upgrade, paid API access, or any other paid dependency. DP-01 is resolved.
- **QC-004**: Principles I, II, VI, VIII, and X remain binding by reference to the constitution
  without introducing implementation choices into this functional specification.
- **QC-005**: History of first-party ratings and retention of imported information MUST
  respect Spotify's conditions; data maintenance and removal requirements must be checked
  during planning without promising unlimited retention of external data.
- **QC-006**: The application MUST use dark mode only and follow the recorded mobile
  [design reference](design-reference.md), with consistent palette, typography, icons,
  sizes, and reusable equivalent controls/layouts across screens. Required screens absent
  from the prototype MUST extend that visual language. Layouts MUST be responsive on
  mobile; prototype dimensions MUST NOT become fixed viewport or content requirements.
  Prototype-only features MUST NOT
  expand scope, and this specification, accessibility, authorization, and repository
  language rules prevail over conflicting visual examples.

### Scope Boundaries

**Included in the revised MVP:** administrator/common profiles; Normal/VIP eligibility;
administrator-triggered email invitations with existing verified-email, explicit single-use
activation and 24-hour rules; normal later authentication and no two-person cap; username
discovery and directional follow/unfollow; private direct recipients and pre-existing
groups with target acceptance, any-member posting and creator-only membership administration;
VIP owned/collaborative playlist links through personal connected import and Normal/VIP
album links through non-linked catalog import; disclosed first-100 album scope; imported frozen occurrence ratings/comments and track
means; separate manual general ratings; favorite/least-liked choices; group collective
results and separate group playlist/album rankings; persistent history, real-time recovery,
responsive dark-only mobile use and home-screen installation.

**Outside this revision:** playback/downloads, public social access, monetization, push,
tags, participant chat, general free-text collection comments, other music services,
personal/public rankings, buying VIP or provider access, automatic source composition sync,
manual track/file upload as an assumed fallback, and additional administrator powers
over other people's private content. Deletion, submission editing, deliberate resubmission,
role-management screens, voluntary group leave/succession and user-selected feed sorting
are not introduced without a separate decision.

This request supersedes playlist-only/connected-only import, the sole-owner invitation
role, manual-delivery-only assumptions, and the previous exclusions of manual general
ratings, collective group grades and rankings. It does not weaken direct-review privacy
or turn a group into a single-collection space.

### Key Entities *(include if feature involves data)*

- **Participant Profile**: identifiable activated account with username, administrative role
  and trusted import eligibility, linked to its shares and authored feedback. Username is
  a unique lowercase ASCII alphanumeric handle, consistently displayed with an @ prefix.
- **Administrator**: role allowed to enter a target email and send account invitations;
  initially provisioned once by a trusted operator without an application invitation;
  not a global reader or the creator of every group. Later promotions require prior
  invitation-based activation.
- **Normal/VIP Eligibility**: import capability distinction for common users. Both have
  the same audience/review permissions; both import albums, while VIP additionally permits
  personal Spotify connection and eligible playlist submission.
- **Account Invitation**: administrator-issued target-email permission with issuer,
  issuance/deadline, sending outcome and protected one-use activation verification.
- **Activation Code**: confidential usable secret represented in application verification
  storage only by protected one-way data; explicit matching verified-email redemption
  activates one account, and later logins do not reuse it.
- **Spotify Connection**: separately authorized personal source access, not proof of app
  membership, administrator authority or unrestricted provider allowance.
- **Follow**: unique directional participant connection used for future recipient selection;
  unfollow leaves old direct grants, reverse follows and groups independent.
- **Private Group**: creator-managed private audience containing multiple playlist and
  album shares. Current members read all history and may post; only creator administers.
- **Group Invitation**: identity-bound pending/accepted/canceled invitation distinct from
  account activation; only target acceptance creates membership.
- **Group Membership**: current/removed relationship. Removal ends access including to
  earlier/authored group feedback, while separate direct grants remain independent.
- **Collection Submission**: one private context-specific share, with playlist/album type,
  authenticated sender, source reference, available identity metadata, time and audience.
- **Import Scope**: captured full or first-100 evaluation boundary, original order,
  known source total when available, imported/rateable counts and cutoff/coverage disclosure.
- **Imported Occurrence**: independently identified appearance in that scope. Duplicate
  songs retain separate positions, ratings/comments and selection identity.
- **Excluded Item**: captured unsupported item with available exclusion reason, omitted
  from rateable totals and mean divisors.
- **Track Feedback**: one reviewer's current occurrence score, optional plain-text comment,
  authorship and version/confirmation state.
- **Collection Review**: one eligible reviewer's feedback within a share, including track
  mean/progress, a separate optional manual general rating, favorite and least-liked choices.
- **Favorite/Least-Liked Choice**: current reviewer selection referencing an eligible
  occurrence in the same frozen collection, with the review's visibility/edit rules.
- **Group Result**: authorized individual review breakdown, current mean of recorded
  manual general ratings and contribution count for one group share.
- **Typed Group Ranking**: that group's playlist or album ordering by group final grade,
  three persistent highlighted positions and remaining compact rows; no cross-context data.
- **Provider Metadata**: available title, artwork, artist/duration/release information
  and source attribution, refreshable/removable under provider conditions rather than a
  permanent archive or proof of private-source ownership.

## Success Criteria *(mandatory)*

### Measurable Outcomes

These are acceptance targets, not completed measurements or membership caps. Use
fictitious prepared data and authorized source samples, at least three activated participants,
recorded network/service conditions and the applicable declared import scope.

- **SC-001**: All activated test participants can access the space and recognize submission
  authorship; 100% of anonymous access, inactive-account, unauthorized-person, and
  ended-session scenarios reveal no private data.
- **SC-002**: In an observed test, participants can share an eligible playlist or album link within 60 seconds without verbal help, under recorded source/network/capacity conditions and with any required connected-path authorization already granted. The non-linked case must not require a personal Spotify connection.
- **SC-003**: A 60-supported-occurrence source imports 100% of its declared full scope, including all required parts. Separate capped tests verify the first-100 rule; a capped set is never labeled the complete original.
- **SC-004**: Track-rating tests produce exactly 2.5, then 2.75, then 3.5 for successive scores 2.5/3/5 and completion at three of three. The duplicate and upward-rounding examples remain exact. Unrated, zero and invalid values are distinguished in every test, independently of a manual general rating.
- **SC-005**: Across ten confirmed changes alternating between three connected authorized test sessions, every permitted track/general/choice/group-result/ranking or membership update appears within five seconds without manual reload under recorded service/network/quota conditions. An outsider receives no private update.
- **SC-006**: After ending/reopening sessions, every authorized test participant retrieves all permitted records in a ten-submission playlist/album dataset, including frozen scope, current track feedback, manual scores and choices, with correct means/progress and no unauthorized history.
- **SC-007**: All eligible test reviewers can rate three tracks, write a comment, and revisit the result
  without verbal help; a score without a comment is also accepted.
- **SC-008**: Critical access, submission, rating, and history flows can be completed on iOS
  and Android, including after home screen installation, without losing essential content
  or actions at a width of 320 px and with text enlarged to 200%.
- **SC-009**: In every connection-loss and recovery scenario, no unconfirmed write appears
  completed, and retrying does not create duplicate ratings or submissions.
- **SC-010**: Changing the tracks of a previously submitted collection on Spotify changes zero
  scores, comments, or items in the list previously imported for that evaluation.
- **SC-011**: Controls and messages in critical flows can be identified and used with a keyboard,
  VoiceOver on iOS, and TalkBack on Android, meeting the constitution's contrast and touch
  criteria, with zero obstacles to completion in these procedures.
- **SC-012**: In every mixed-import test case, 100% of exclusions are reported and only
  importable track occurrences enter the total; all submissions without rateable
  occurrences are rejected with an explanation.
- **SC-013**: In all invalid-code, reused-code, wrong-recipient, and unverified-email activation
  tests, zero unauthorized accounts activate; rejected wrong-recipient and unverified-email
  attempts consume zero invitations. With the service available, concurrent redemption of
  one valid code by the eligible recipient produces exactly one activation.
- **SC-014**: At least three participants with separate valid private invitations can activate
  accounts and access their authorized content; the third is not rejected because two are active.
- **SC-015**: Every permitted administrator invitation creation targets the specified email, every non-administrator issuance attempt is denied, and previewing/opening causes zero activations/code consumption. Sending succeeds only with an actual confirmed sending outcome; failure is distinguished from success.
- **SC-016**: Every failed sign-in preserves the prior account activation and invitation
  states: wrong passwords never deactivate active accounts or reset consumed codes.
  Failed activation of an inactive account leaves it inactive and preserves the code's
  prior unused/consumed state; attempts on an active account do not consume another code.
  An otherwise valid invitation is accepted before its 24-hour
  deadline; 100% of attempts at or after that deadline are rejected with zero activations
  and zero code consumption. Previewing the link changes its expiry by zero seconds.
  All subsequent sign-ins to an activated account require zero additional invitations.
- **SC-017**: In all configured attempt-limit tests, further attempts during the restricted
  period are denied without activation or code consumption. Inspection of persisted
  invitation verification data and logs reveals zero original usable activation codes.
- **SC-018**: Successful direct-sharing tests cover both one selected followed participant and
  at least two selected followed participants; 100% can access the collection and submit an
  eligible review, while the sender can access the submission.
- **SC-019**: Across feed, history, direct-link, and live-update tests, 100% of attempts by
  activated participants outside the direct or group audience, including non-selected followed
  people and followers of recipients, reveal zero private data from the share.
  Forwarding a link grants zero additional authorizations.
- **SC-020**: In all member-discovery test cases, searching a known username identifies
  the intended activated account, including equivalent bare/leading-@ and ASCII-case
  variants. Every invalid special-character/whitespace/Unicode handle is rejected, and
  empty/lone-@ searches return zero results. All identity labels show exactly one @ prefix.
  Following the selected UUID from the results adds exactly one entry
  to the follower's sharing list without a reciprocal follow. Search and follow-list
  inspection expose zero private collection, review, activation-code, or credential data.
- **SC-021**: In all direct-share tests with multiple recipients, the sender can read each
  review and each recipient can read only their own review; attempts to read another
  direct recipient's scores, comments, average, or progress disclose zero private review data.
- **SC-022**: Group-share tests include successful submissions by the creator and a current
  non-creator member. Every tested posting attempt by a pending invitee, removed member,
  or other non-member creates zero group submissions. All authorized current group members
  can read every review for the share, including its author, average, and progress, while
  non-members receive zero private review data.
- **SC-023**: Tests that share the same Spotify collection through direct and group contexts
  reveal zero direct-review records through group access. All attempts to write on
  another reviewer's behalf are rejected.
- **SC-024**: In all group-administration tests, only the creator can invite or remove
  members; non-creator members, outsiders, and an administrator without the creator
  role are denied. Invitations to people the creator does not follow are rejected, and
  wrong-account invitation acceptance grants zero memberships.
- **SC-025**: Pending group invitations grant zero membership-based access to private
  content. After acceptance, the invited account can access both existing and future group
  shares and reviews as a current member.
- **SC-026**: Repeated or concurrent acceptance of one group invitation creates exactly
  one membership for the intended account and consumes zero account-activation codes.
- **SC-027**: New members can retrieve 100% of a known group-history test set created before
  they joined, while pending invitees can retrieve zero private records from that set.
- **SC-028**: After confirmed removal, 100% of former-member requests for private group
  content are denied, including historical links and authored reviews. Connected group
  views stop displaying that private content within five seconds under documented conditions.
- **SC-029**: Group removal leaves 100% of independently authorized direct shares unchanged
  and preserves the group's historical reviews for current members, with zero direct-review
  records exposed through the group.
- **SC-030**: Every connected-path attempt lacking required provider allowlisting or
  authorization explains that limitation and creates zero completed connected imports.
  Application activation/VIP alone grants no provider permission; the separate eligible
  non-linked path remains available under its own source rules.
- **SC-031**: In all unfollow tests, the unfollowed person is absent from the follower's
  recipient list for new direct shares and cannot be selected until followed again.
  Unfollowing changes zero existing direct-share permissions, saved reviews, reverse
  follows, or private group memberships.
- **SC-032**: Edit tests cover partial and completed reviews. Replacing 2 with 5 in a
  completed two-track review with scores of 2 and 4 produces a track mean of 4.5, two of two rated, and
  complete status; a comment-only edit changes zero scores or summary values. All invalid,
  unauthorized, and stale conflicting edits leave confirmed state unchanged. Confirmed
  edits persist across sessions and reach authorized viewers within the five-second
  conditions of SC-005, without exposing feedback to unauthorized viewers.
- **SC-033**: Role tests cover initial administrator bootstrap without an application invitation, same-identity no-op retry, rejection of a different/unverified bootstrap identity and denial of bootstrap to application callers. Every subsequent account requires invitation activation. All unauthorized administrator/VIP self-promotion, Normal personal-connection and Normal playlist-submission attempts fail, including for a Normal administrator; both common-user subtypes retain the same permitted album/social/review/result/ranking features. Every identity-policy example passes signup/setup, search and verified-email matching checks as applicable.
- **SC-034**: A Normal participant can import/share an eligible Spotify album without a personal Spotify connection. Every Normal playlist-submission attempt is denied without creating a share, while authorized received-playlist review remains available; no borrowed personal grant is used.
- **SC-035**: Playlist and album metadata tests display the returned title/cover/artist fields correctly. Missing optional artwork/duration/artist data produces zero fabricated values and preserves identifiable source/submitter labels.
- **SC-036**: Album tests preserve 100% of the ordered eligible occurrences in the declared scope, including multi-disc/part boundaries and duplicates, and apply the same private evaluation rules as playlists.
- **SC-037**: For a prepared 120-occurrence album catalog source, exactly the first 100 are the frozen input before eligibility exclusions, the cutoff is visible, and the omitted 20 are never shown as imported or rated. Unknown original totals are explicitly unknown.
- **SC-038**: Manual-rating tests keep a track mean of 4.5 when a general score of 2 is saved; a general-score edit changes no occurrence score or track-progress count. Unset and manual zero remain distinct in every case, and invalid general scores produce no persisted or aggregate change.
- **SC-039**: Favorite/least-liked tests associate each saved choice with the correct reviewer and same-share occurrence, preserve it across sessions and reject every foreign-occurrence/unauthorized/stale write without changing scores.
- **SC-040**: Group-result tests produce 3.5 from manual scores 5/2 with an unscored member excluded, then 2.33 when a third recorded score is 0. Unequal track-rating counts change neither weighting nor the correct numeric outcome.
- **SC-041**: For both collection types, ranking tests cover zero, one, two, three and four eligible entries: exactly three highlighted positions remain visible, the highest available entries occupy them and fourth/later entries appear below in descending order.
- **SC-042**: A real zero-scored collection receives an eligible ranking position, while an entry without a collective mean receives no fake numeric grade; every populated position's numeric grade and accessible star representation identify the same result.
- **SC-043**: Confirmed general-rating edits recompute both group grades and affected ranking order within the existing five-second conditions, with no duplicate votes, mixed-context feedback or stale-value regression.
- **SC-044**: All nonmember/pending/removed/unauthorized administrator group-result and ranking requests disclose zero private records; removed connected views are cleared under SC-028 conditions and independent direct shares remain unchanged.
- **SC-045**: Every invitation-send failure test reports no false delivery/activation, consumes zero codes and preserves the original issuance deadline on retry.

## Assumptions

### Confirmed Product Decisions

- **D-001**: Membership is not limited to two participants. Private invitations and single-use
  activation codes are required for every account except the restricted initial
  administrator provisioned by the trusted operator under constitution v4.0.0.
- **D-002**: MVP collections come from Spotify playlists and albums. VIP participants submit owned/collaborative playlists through their personal authorized connection. Normal and VIP participants import albums through the non-linked catalog path with a disclosed first-100 boundary.
- **D-003**: Each eligible reviewer rates occurrences and receives a separate track-based arithmetic mean. A manual general collection rating is independent and does not replace that derived mean.
- **D-004**: The track mean is partial from its first saved occurrence and complete when all imported rateable occurrences are rated. Manual rating and group-score states remain distinct.
- **D-005**: Tags were removed from the product. Comments and highlights are optional text
  alongside each track's score, without a separate conversation or overall playlist comment.
- **D-006**: The requester already holds personal Spotify Premium. The owner-requested
  exception in constitution v4.0.0 permits using the application owner's existing
  subscription without incremental project expense; infrastructure remains free.
- **D-007**: Connected playlist imports require VIP eligibility and the submitter's own owner/collaborator authorization. Albums are catalog releases imported without a participant's personal connection. Normal playlist submission is outside this MVP.
- **D-008**: The declared imported occurrence scope is preserved against later source composition changes, including the disclosed first-100 album scope.
- **D-009**: Occurrence and manual general scores use 0–5 half-steps. Derived means display up to two decimals with nearest-hundredth upward ties, without intermediate rounding.
- **D-010**: Repeated tracks receive one score per occurrence; all rated occurrences
  enter the average with equal weight.
- **D-011**: Only importable tracks are rated, with exclusions explicitly reported;
  playlists without any rateable occurrences are rejected.
- **D-012**: Administrators may enter an intended recipient email and send a private activation invitation. Common Normal/VIP users cannot issue account invitations; administrative invitation authority is not a global content permission.
- **D-013**: Invitation redemption follows authentication and an explicit activation action.
  Opening a link does not redeem it; activation and consumption succeed together, and
  later sign-ins do not require another invitation.
- **D-014**: Codes are unguessable, expire, are stored only as protected one-way verification
  information, and are protected against guessing attempts. Each invitation is bound to
  the intended recipient email specified by the administrator and can be redeemed only by a
  signed-in account with that verified email. It expires 24 elapsed hours after issuance
  and cannot be redeemed at or after that deadline.
- **D-015**: Participants follow people and share playlists directly with one or more
  selected people they follow, or with a previously created private group.
  A common application-wide feed is not the sharing model.
- **D-016**: Account activation invitations, directional follows, direct recipient selection,
  and private group membership are distinct concepts with separate permissions.
- **D-017**: Participants find people by username through an in-app search menu. Following
  a selected account adds it to the follower's sharing list without requiring a follow back.
  Unfollowing removes eligibility for new direct shares until followed again, preserving
  existing direct-share permissions and reviews, reverse follows, and group memberships.
- **D-018**: Direct feedback, including manual ratings and choices, remains private between the sender and that reviewer. Group collective results never merge direct-share feedback.
- **D-019**: Any current member may share playlists/albums in a private group created beforehand. Current members read individual reviews and collective results; the group score is the equal-weight mean of recorded manual general ratings.
- **D-020**: Only a private group's creator manages its membership. The creator invites
  people they follow, and each person must accept before joining.
- **D-021**: Group invitations target existing activated accounts and are separate from
  account-activation invitations and codes; a pending group invitation grants no group access.
- **D-022**: Current group members see the entire group history. Removed members lose group
  access, including to earlier content and their authored group reviews; independent
  direct shares remain separate, and group review history is preserved for current members.
- **D-023**: The connected playlist pilot stays within the initial allowance of up to five authorized Spotify accounts; VIP does not bypass provider eligibility. Normal participants import catalog albums and may receive/review playlists, with no product membership cap.
- **D-024**: Eligible reviewers may edit their own current track/general feedback and choices while authorized, even after track completion. Each edit updates only its relevant derived values and preserves independent progress/privacy.
- **D-025**: The mobile prototype is the visual reference; the repository governs product
  behavior. The application uses dark mode only, preserves the supplied brand assets,
  and reuses a consistent visual system across existing and newly designed screens.
  Prototype measurements guide proportions; responsive mobile usability takes precedence
  over reproducing a fixed frame size.
- **D-026**: Profiles distinguish administrator/common-user authority; common users distinguish VIP/Normal import eligibility. Personal Spotify connection and eligible playlist submission are VIP-only; administrator authority grants neither by itself.
- **D-027**: The non-linked path uses an album link without the participant connecting Spotify. Beyond 100 source occurrences, import the first 100 and identify the cutoff; no non-linked playlist path is included.
- **D-028**: Albums have the same private sharing/evaluation experience as playlists, rather than a separate permission or scoring system.
- **D-029**: A manual 0–5 half-step general rating is added alongside, and independent from, the mean of occurrence scores.
- **D-030**: Reviewers can choose a favorite and a least-liked imported occurrence, under their own feedback identity and audience permissions.
- **D-031**: The group final score averages recorded manual general ratings equally; absent ratings are excluded and zero is included.
- **D-032**: Rankings are collective per group, with one ranking for playlists and another for albums, using the group final score.
- **D-033**: Each ranking always shows three highlighted positions with identity, numeric grade, stars and place when populated; unavailable entries leave visible empty slots and fourth/later positions appear below.
- **D-034**: Public-sample preview/title/artwork/track visibility is verified research evidence, not a stable importer contract, authorization waiver or universal provider promise.

### Explicit Working Defaults

These are disclosed working assumptions, not additional requester-confirmed decisions.
They are reviewable during clarification/planning and must not be presented as answers
the requester supplied.

- **A-01 — Role setup:** trusted application configuration assigns administrative and VIP
  authority. New common accounts default to Normal unless that trusted authority assigns
  VIP. No self-promotion or additional role-management screen is implied. Administrator
  invitation authority does not itself provide personal Spotify authorization.
- **A-02 — General feedback timing:** manual general ratings and choices are optional
  until supplied and editable under existing review permissions. They do not require a
  new final-submit/reveal gate or change track completion. An absent manual score is
  excluded from group means, as explicitly confirmed.
- **A-03 — Choice edge cases:** the proposed default is at most one favorite and one
  least-liked occurrence, distinct when both are set. With one eligible occurrence,
  allow one choice and leave the other unset rather than requiring an impossible pair.
  No extra score change or choice-based weighting is introduced.
- **A-04 — Ranking eligibility and unit:** a group-specific submission is rankable once
  it has a recorded manual contribution, even if its track reviews are partial. It remains
  separate from another share of the same source. Show contributing-rating count and
  capped-scope context; do not wait silently for every member or merge private contexts.
- **A-05 — Ties:** equal exact means retain a deterministic stable order without changing
  grades or creating more than three highlighted slots. The technical stable ordering
  key belongs in the plan; no popularity or VIP preference is introduced. Compare means
  consistently before display rounding.
- **A-06 — Retained former-member reviews:** because removal preserves feedback for
  current group readers, retained manual ratings continue to contribute while lawfully
  retained. Removal revokes the person's access, not their historical vote automatically.
  A different contribution policy requires an explicit revision.
- **A-07 — Catalog-source boundary:** non-linked imports use the permitted album catalog
  source; they do not retrieve private playlists or assert playlist ownership from a link.
  Connected owner/collaborator restrictions apply to VIP playlist imports. Actual album
  access and scope remain mandatory integration acceptance checks, not an assumed bypass.
- **A-08 — Source item boundary:** the first-100 boundary is evaluated in source order
  before reporting unsupported-item exclusions; the resulting rateable count can be
  below 100. Do not silently fetch beyond the captured boundary merely to fill a quota.
  This capped non-linked method applies to albums; connected playlists retain full-scope import.
- **A-09 — Album resources:** album-link support follows the provider's catalog resource;
  no new product filter excluding singles/compilations or a separate ranking for them
  is introduced. Available release type should not be fabricated.

### Dependencies and Verified Constraints

[import-feasibility.md](import-feasibility.md) separates documented provider capabilities,
observed public responses and unverified production feasibility. Preview metadata can
include title/artwork; it is not a tracklist contract. Public playlist/album examples
displayed tracks/artists/durations, but this alone establishes neither stable automated
import nor permission to operate it. The 100-occurrence cutoff is the accepted product
scope policy for the alternative path, not a claimed universal provider entitlement.

Connected Spotify eligibility/allowlisting and the developer-owner's existing Premium
prerequisite remain separate from application roles and unlimited-by-two membership.
VIP designation does not grant external access. Album catalog data must not inherit a
playlist-owner requirement. No additional paid dependency or source-access workaround
is assumed authorized.

The current technical plan selects documented source routes and defines source-specific
freshness/cleanup, invitation delivery, capacity and zero-cost checks. Actual account/source
access, mail delivery and complete application acceptance remain implementation/release
evidence. A source or delivery failure must be explained, never presented as false success.

### Policies Not Added and Planning Readiness

The three primary questions asked during this revision have requester answers recorded
above. There are no unresolved primary clarification markers. Explicit working defaults
remain visible for review rather than being attributed to the requester.

**DP-06 — Broader playlist import:** expansion beyond the provider's connected pilot or
adding Normal playlist submission remains a future decision requiring a usable permitted
source and explicit scope approval. No older Mayonnaise Spotify app exists. Extra client
IDs, another person's credentials, paid extraction or a file/manual fallback are not added.

Deletion, editing/resubmitting submissions, special feed sorting, custom comment-length
limits, prior-version audit, extra administrator powers and personal/public rankings
remain unspecified/unapproved. This is not a permanent prohibition if requested later.

The specification, revised technical design and 136-task list are ready for consistency
analysis before implementation. Generated task/field coverage is not completed application
validation. Full source/delivery/application acceptance and all constitutional release
gates remain mandatory.
