# Feature Specification: Mayonnaise — Share and Rate Spotify Playlists

**Feature Branch**: `main` — existing branch; no branch-creation hook is configured.

**Feature Directory**: `specs/001-rate-playlists`

**Created**: 2026-11-18

**Status**: Draft — clarification complete; ready for technical planning of the Spotify import pilot.

**Input**: A private, collaborative, mobile-first application for invited participants to share
Spotify playlists, rate their tracks, and follow results in real time. The requester's
subsequent decisions include integration to import tracks, removal of tags, a partial
overall average from the first rating, optional comments per track, and preservation
of the track list imported at submission time. Participants share privately with one or
more selected people they follow; account activation and following do not grant access
to unshared playlists. Participants find people through an in-app username search menu.
Direct-share reviews are private between the sender and each reviewer. Participants may
also create private groups and share with a previously created group, whose members
can see the reviews of playlists shared there. Current group members can read the complete
history, including content predating their membership; removed members lose group access.

**Governance**: [Mayonnaise Constitution, v3.0.0](../../.specify/memory/constitution.md).
The principles cited in this specification are acceptance obligations; solution choices
remain subject to constitutional review during planning.

## Clarifications

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

## User Scenarios & Testing *(mandatory)*

Priorities indicate delivery order, not exclusion of features from the MVP. Each story
can be verified using prepared participants, playlists, and test ratings, without depending
on implementation of the other stories for its demonstration.

### User Story 1 - Access an Identified Private Space (Priority: P1)

As an authorized participant, I want to access my account and authorized private playlist
shares and recognize the participants involved so that I can share ratings privately. As the designated
application owner, I want to issue account-activation invitations and share them privately
so that joining remains controlled.

**Why this priority**: Privacy and identification are prerequisites for all interactions.
Addresses principles IV and VII.

**Independent Test**: Prepare at least three invited participants with separate activation
codes, the designated owner, an ordinary participant, an inactive account, an unauthorized
person, and a test playlist; verify invitation issuance, explicit single-use activation,
authenticated access, verified recipient email matching, expiry, failure recovery, and access denials.

**Acceptance Scenarios**:

1. **Given** a participant activated through a private invitation is unauthenticated, **When** they
   complete access with valid identification, **Then** they see their authorized private space and their identity.
2. **Given** an unauthenticated, inactive, or unauthorized person, **When** they attempt to access
   a playlist, rating, or update from the space, **Then** no private data is displayed.
3. **Given** an authenticated participant, **When** they submit a playlist or a rating,
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
9. **Given** the designated application owner, **When** they specify the intended recipient's
   email and issue an account-activation invitation, **Then** they receive a new private
   code or invitation link bound to that email to share manually with its recipient.
10. **Given** an ordinary participant or unauthenticated person, **When** they attempt to
    issue an account-activation invitation, **Then** creation is denied and no activation code is disclosed.
11. **Given** a valid unused invitation link, **When** it is opened or previewed without
    explicit activation, **Then** the code remains unused and no account is activated.
12. **Given** an unused invitation and an unauthenticated recipient, **When** they try to
    activate an account, **Then** identity verification is required before activation;
    a failed sign-in leaves the invitation unused.
13. **Given** a signed-in recipient and a valid unused invitation, **When** activation fails
    before it completes, **Then** the account remains inactive and the code remains unused,
    allowing a retry while the invitation is still valid.
14. **Given** an account already activated by invitation, **When** its participant signs in
    again or on another device, **Then** ordinary authentication grants authorized access
    without requesting or consuming another invitation.
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

---

### User Story 2 - Find People to Follow and Share Playlists Privately (Priority: P1)

As a participant, I want to find people by username, follow them, and select one or more
people from my following list to privately receive a Spotify playlist I own or collaborate
on, so that the selected recipients can find its tracks and rate them. I also want to create
a private group beforehand and choose a group I currently belong to when sharing for
group-visible reviews. Any current member can contribute a playlist. As its creator,
I invite people I follow and manage membership; invitees join only after acceptance.
I can unfollow someone to stop selecting them for new direct shares while preserving
our existing direct shares and reviews.

**Why this priority**: Provides the content for the experience and avoids manual track entry.
Addresses principles I, III, IV, and VII.

**Independent Test**: Prepare a sender, two followed participants selected as recipients,
a followed participant who is not selected, and another activated participant. Verify
username search, one-way following, the sharing list, and playlist import while denying
private access outside the direct audience. Also prepare a previously created private group
with a sender and two reviewers, plus a non-member, to verify group sharing and visibility.
Include pending group invitees, removed members, and a non-creator member to verify
posting permissions separately from acceptance and membership administration.

**Acceptance Scenarios**:

1. **Given** an authorized sender, selected followed participants, and an accessible owned or collaborative
   playlist, **When** they provide a valid link and complete import and private sharing,
   **Then** the sender and selected recipients can find the share with its playlist identification,
   link, submitter, and imported track occurrences.
2. **Given** a playlist whose tracks are delivered in multiple parts by the external service,
   **When** import finishes, **Then** all parts have been considered, importable track
   occurrences are displayed, and excluded items are reported; the first part alone
   is not presented as the complete list.
3. **Given** an invalid link, another content type, or a playlist without read permission,
   **When** the participant attempts to share it, **Then** they receive an explanation
   and no incomplete list is announced as successfully imported.
4. **Given** a submitted playlist, **When** an authorized participant opens its link,
   **Then** listening takes place in the external service, without playback inside Mayonnaise.
5. **Given** an imported playlist, **When** its track list changes on Spotify,
   **Then** the list used for that evaluation remains the one captured at submission time.
6. **Given** a playlist with two importable tracks and two incompatible items,
   **When** import finishes, **Then** the two track occurrences can be rated,
   the two excluded items are indicated, and the total number of rateable occurrences is two.
7. **Given** an empty playlist or one with no importable tracks, **When** the participant
   attempts to share it, **Then** submission is rejected with an explanation and does not
   create a rateable playlist in the feed.
8. **Given** a playlist imported from Spotify, **When** authorized participants view its
   information, **Then** they can identify Spotify as the source and open the corresponding content.
9. **Given** a sender following multiple people, **When** they select one person for a direct share,
   **Then** only the sender and that recipient are within its authorized audience.
10. **Given** a sender who selects two or more people they follow, **When** the direct share completes,
    **Then** every selected recipient can access the playlist and can submit an eligible review.
11. **Given** an activated participant outside the share, including a non-selected followed
    person or someone following a recipient, **When** they try to find the share in a feed,
    history, a direct link, or live updates, **Then** no private playlist or review information
    is disclosed to them.
12. **Given** an attempt to select a new direct recipient the sender does not follow,
    **When** sharing is requested, **Then** that recipient is rejected and access is not granted.
13. **Given** a forwarded Mayonnaise link, **When** someone outside the selected audience
    opens it, **Then** the link does not grant access to the private share.
14. **Given** an activated participant and another member's username, **When** they search
    in the application's search menu, **Then** they can identify the matching member and
    follow them without seeing private playlist or review data in the search results.
15. **Given** participant A follows participant B and B does not follow A, **When** A chooses
    playlist recipients, **Then** B appears in A's sharing list and can be selected.
16. **Given** B follows A but A does not follow B, **When** A views their sharing list,
    **Then** B is not eligible solely because B follows A.
17. **Given** an existing follow from A to B, **When** A repeats the same follow action,
    **Then** B appears once in A's following and sharing lists.
18. **Given** an activated participant, **When** they create a private group, **Then** the
    group has its own identity and a recorded creator, distinct from application ownership.
19. **Given** a previously created private group containing its creator and other current
    members, **When** any current member, including a non-creator, shares an eligible
    playlist to that group, **Then** authorized group members can access the playlist and
    its group-visible reviews, while non-members cannot.
20. **Given** a group creator who follows an activated participant, **When** the creator
    invites that participant, **Then** an invitation addressed to that account is pending;
    the account does not become a member automatically.
21. **Given** a non-creator member, outsider, or application owner who is not the group
    creator, **When** they attempt to invite or remove members, **Then** the action is denied.
22. **Given** a pending group invitation, **When** its recipient tries to access group
    playlists or reviews before accepting, **Then** the invitation alone grants no access.
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
28. **Given** a group containing earlier playlists and reviews, **When** an invited person
    accepts and joins, **Then** they can read that earlier group content as a current member.
29. **Given** a current group member, **When** the creator confirms their removal,
    **Then** that person loses access to all group playlists and reviews, including
    earlier content and reviews they authored.
30. **Given** a removed group member who also has independent direct shares, **When** they
    access those direct shares, **Then** their independent permissions are unchanged and
    the group's historical reviews remain available to its current members.
31. **Given** an activated participant whose Spotify account is not allowlisted for the pilot,
    **When** they attempt to import a playlist, **Then** the integration access limitation
    is explained and no incomplete submission is published as successfully imported.
32. **Given** a pending invitee, removed member, or other non-member, **When** they attempt
    to share a playlist with the private group, **Then** posting is denied and no group
    submission is created; application ownership alone does not permit posting.
33. **Given** participant A previously shared a playlist directly with participant B,
    **When** A unfollows B, **Then** B leaves A's list for new direct shares, while both
    retain their existing direct-share permissions and reviews. A cannot choose B for a
    new direct share until following B again; any reverse follow or group membership is unchanged.

---

### User Story 3 - Rate Tracks and Follow the Playlist Score (Priority: P1)

As a selected recipient receiving a playlist, I want to assign a score to each track occurrence and follow
an overall average so that I can express my assessment progressively. I can revise my
own saved ratings and comments while authorized, including after completing the review.

**Why this priority**: This is the product's core function. Addresses principles IV, V, and VII.

**Independent Test**: Prepare direct and group shares with two reviewers and identified
tracks. Record distinct scores for each reviewer to verify separate averages and progress,
direct-review isolation, group-review visibility, authorship permissions, and edits to
both partial and completed reviews.

**Acceptance Scenarios**:

1. **Given** a playlist with no ratings, **When** the recipient opens it, **Then** they see
   that it has not been rated; the absence of a rating is not displayed as a zero score.
2. **Given** a playlist with three distinct tracks, **When** the recipient saves a score
   of 2.5 for the first track, **Then** the displayed average is 2.5, marked as partial,
   with one of three tracks rated.
3. **Given** the previous scenario, **When** the recipient saves a score of 3 for the second
   track, **Then** the partial average becomes 2.75, with two of three tracks rated.
4. **Given** the previous scenario, **When** the recipient saves a score of 5 for the third
   track, **Then** progress shows three of three and the evaluation is complete;
   the average is 10.5 divided by 3, which is 3.5.
5. **Given** the submitter or someone outside the authorized sharing context, **When** they attempt to record a rating
   for that playlist, **Then** the action is denied and the ratings remain unchanged.
6. **Given** a score outside 0 to 5 or outside 0.5 increments, **When** the person attempts
   to save it, **Then** they receive a validation message and no invalid rating is recorded.
7. **Given** a playlist with two occurrences of the same track, **When** the recipient
   saves 4 for one occurrence and 2 for the other, **Then** both count separately,
   progress is two of two, and the completed average is 3.
8. **Given** four occurrences with scores of 0, 0, 0, and 0.5, **When** the average is displayed,
   **Then** the average of 0.125 is shown as 0.13 without changing the original scores.
9. **Given** a direct share sent to reviewers B and C, **When** B saves a score or comment,
   **Then** only B and the sender can see B's review and its average and progress;
   C can access the playlist and C's own review without seeing B's feedback.
10. **Given** a playlist shared with a private group, **When** a group reviewer saves a score
    or comment, **Then** authorized group members can read that review with its author,
    average, and progress, but cannot submit scores on that reviewer's behalf.
11. **Given** a completed review with track scores of 2 and 4, **When** its authorized
    reviewer changes the first score to 5, **Then** the confirmed average becomes 4.5,
    progress remains two of two, and the review remains complete without a duplicate rating.
12. **Given** a partial review with one of three occurrences rated 2.5, **When** its reviewer
    changes that score to 0, **Then** the average becomes 0, remains partial, and progress
    remains one of three. An invalid replacement leaves the saved score and average unchanged.
13. **Given** a saved rating, **When** another reviewer, the playlist sender, an application
    owner without reviewer eligibility, or a removed group member attempts to edit it,
    **Then** the edit is denied and the confirmed rating and summary remain unchanged.

---

### User Story 4 - See Updates Without Reloading and Recover Connectivity (Priority: P1)

As a participant, I want new submissions, ratings, comments, and averages to update
automatically so that I can follow other participants' activity.

**Why this priority**: Maintains a reliable shared view. Addresses principles IV and V.

**Independent Test**: Open authorized test sessions for the sender and selected recipients
plus an activated participant outside the share; trigger new records, connection loss,
reconnection, and retries, verifying that live updates respect the private audience.

**Acceptance Scenarios**:

1. **Given** the authorized test sessions are connected and the service is available within its quotas,
   **When** a playlist, rating, or comment is confirmed in one session, **Then** every other session
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

---

### User Story 5 - Comment on Tracks and Record Highlights (Priority: P2)

As a reviewer, I want to write an optional comment alongside a track occurrence's rating
so that I can record impressions and highlight what caught my attention.

**Why this priority**: Adds context to scores without creating a separate conversation.
Addresses principles I, III, IV, and IX.

**Independent Test**: Prepare a rateable track and save a score with the comment
“I liked the bass in this track”; verify its association, authorship, and visibility
to authorized viewers.

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

### User Story 6 - Revisit Shared History (Priority: P2)

As a participant, I want to view submitted playlists and their previous ratings so that
I can revisit tracks, scores, comments, and each playlist's completion progress.

**Why this priority**: Preserves the value of ratings after submission.
Addresses principles V and VII.

**Independent Test**: Prepare multiple submissions and ratings, end the session, and return;
compare the retrieved records with the known test data.

**Acceptance Scenarios**:

1. **Given** playlists submitted at different times, **When** a participant views history,
   **Then** they find the submissions with authorship and their respective ratings.
2. **Given** a partially rated playlist, **When** it is viewed in another session,
   **Then** confirmed scores, comments, the partial average, and progress are preserved.
3. **Given** a completely rated playlist, **When** it is reopened later,
   **Then** it shows the imported list, per-track scores, and the corresponding completed average.
4. **Given** a space with no submissions, **When** the feed or history is opened,
   **Then** it presents an understandable empty state and the option to share a playlist.
5. **Given** group history created before a participant joined, **When** that participant
   becomes a current member, **Then** the group history is accessible without a join-date cutoff.
6. **Given** a participant removed from a group, **When** they try to reopen its history
   through a feed, history view, or saved link, **Then** private group content is denied.
7. **Given** a confirmed rating or comment edit, **When** an authorized participant returns
   in a new session, **Then** history shows the latest confirmed values and corresponding
   average and progress; a prior-version audit view is not required.

---

### User Story 7 - Use and Install on Mobile (Priority: P2)

As a participant, I want to use Mayonnaise on my phone and add it to the home screen
so that I can easily open my private space on iOS and Android.

**Why this priority**: Supports the primary usage context. Addresses principles III and IX.

**Independent Test**: Perform access, submission, rating, and history lookup on each platform;
install through the available option and reopen the application from the home screen.

**Acceptance Scenarios**:

1. **Given** a compatible iOS or Android phone, **When** the person installs the application
   through the available flow, **Then** they can open it from the home screen.
2. **Given** the application was opened from its icon, **When** the person accesses their
   space, **Then** the same permissions and shared information are respected.
3. **Given** a screen 320 px wide or text enlarged to 200%, **When** the person completes
   the primary flows, **Then** essential content and actions remain usable.
4. **Given** touch, keyboard, or screen reader navigation, **When** the person selects
   a score and views comments, **Then** they can identify controls, values, and messages.

### Edge Cases

- **Incorrect link or unsupported content:** explain the problem without publishing incorrect
  content; the MVP accepts Spotify playlist links, not track, album, or profile links.
- **Third-party playlist without collaboration:** explain the access limitation; do not promise
  universal import just because a link opens on Spotify.
- **Denied, expired, or revoked Spotify authorization:** explain the condition and permitted
  next step without exposing credentials or treating import as completed.
- **Spotify account outside the pilot allowlist:** explain the integration access limitation;
  an account-activation invitation does not authorize Spotify API access, and a refused
  import must not create a completed submission.
- **Quota, unavailability, or interrupted import:** indicate failure or progress and prevent
  an incomplete list from being treated as the final list for evaluation.
- **Existing personal Premium unavailable:** explain the affected integration's unavailability
  and degrade gracefully; do not buy, renew, or upgrade a subscription with project funds.
- **Empty playlist or no rateable occurrences:** reject submission with an explanation,
  without calculating an average or presenting division by zero as a score.
- **Non-importable track, local item, episode, or unidentified item:** exclude it from evaluation
  and report the items and available reasons, without automatic scores or silent exclusion.
  Playback unavailability alone does not establish that metadata cannot be imported;
  the criterion is whether the track can be identified and imported.
- **Repeated track:** each occurrence receives its own rating and participates separately
  in the average and progress, even when the musical content is the same.
- **Playlist changed during import:** do not announce completion of a list that mixes
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
  to that playlist's private submission, ratings, comments, or updates.
- **Someone following a recipient:** follow relationships do not transitively grant access.
- **Unfollowing an existing direct recipient:** remove them from eligibility for new direct
  shares until followed again, while preserving existing direct-share access and reviews.
  The reverse follow and private group memberships are independent.
- **Multiple direct recipients:** each can access the playlist and their own feedback;
  another recipient's scores, comments, average, and progress remain private.
- **Private group share:** authorized group members can read all reviews for that group's
  share; membership does not authorize impersonating another reviewer.
- **Same Spotify playlist in different contexts:** sharing it with a group does not expose
  reviews previously submitted through a separate direct share.
- **Pending group invitation:** membership and private group access are not granted before
  the intended account explicitly accepts.
- **Group posting without current membership:** pending invitees, removed members, and
  other non-members cannot post playlists to the group; previous authorship and application
  ownership do not replace current membership.
- **Unauthorized group administration:** ordinary members and an application owner who is
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
  original Spotify playlist's visibility or grant Spotify collaboration permissions.
- **Forwarded account-activation code or unverified email:** a usable code alone grants no
  activation. The signed-in account must have the verified email specified by the owner
  for that invitation; rejection leaves the account inactive and the invitation unused.
- **Invitation preview or repeated page load:** opening, previewing, or refreshing an
  invitation does not redeem it; activation requires an explicit action after sign-in.
- **Expired, invalid, or consumed invitation:** deny activation without granting private
  access. Account-activation invitations expire 24 elapsed hours after issuance and are
  invalid at or after that deadline; opening the link does not extend their lifetime.
- **Failed sign-in or interrupted activation:** a failed activation does not consume the
  invitation or grant membership. Retrying must not create duplicate activations.
- **Concurrent invitation redemption:** account activation and code consumption form one
  indivisible operation; at most one account can redeem the same invitation.
- **Repeated sign-in after activation:** use normal authentication without asking for a new code.
- **Attempted invitation creation by a non-owner:** deny issuance and protect invitation secrets.
- **Activation-code guessing:** enforce a finite attempt limit and retry window defined
  during planning, without exposing codes in logs or consuming them on rejected attempts.

## Requirements *(mandatory)*

### Functional Requirements

- **FR-001**: The system MUST restrict private access to authenticated participants whose
  accounts have been activated through a private invitation and a single-use activation code,
  with recognizable identities and no fixed two-participant business limit.
- **FR-002**: The system MUST prevent reading, writing, and following private updates by
  anonymous people, inactive accounts, expired sessions, or unauthorized people.
- **FR-003**: The system MUST attribute submissions, ratings, and comments to the authenticated
  identity; one participant cannot impersonate the other.
- **FR-004**: The system MUST allow sessions to be ended and prevent exposure of private
  data after session termination or expiration.
- **FR-005**: The system MUST allow sharing a Spotify playlist link submitted by a participant
  who owns or collaborates on that playlist.
- **FR-006**: The system MUST obtain playlist identification through the authorized integration
  and consider all its content to form the list of importable track occurrences, without
  requiring manual track entry or omitting parts that have not yet been imported.
- **FR-007**: The system MUST distinguish ongoing, completed, and failed imports;
  a result may be announced as complete only when its completeness has been confirmed.
- **FR-008**: The system MUST explain invalid links, insufficient permissions, and external
  service failures understandably, allowing another attempt when applicable.
- **FR-009**: Each participant's feed MUST contain only submissions they are authorized to
  access as direct-share sender, selected direct recipient, or current group member,
  showing playlist identification, external link, submitting participant, submission time,
  and applicable evaluation status. Authorship alone MUST NOT retain group access after removal.
- **FR-010**: The system MUST allow opening the playlist in the external service;
  the application does not play or download track audio.
- **FR-011**: The system MUST preserve the track list imported at submission time
  without synchronizing subsequent changes to the Spotify playlist's composition.
- **FR-012**: The system MUST allow selected direct recipients and eligible members of a
  group receiving a playlist to rate its track occurrences. The sender and participants
  outside the authorized sharing context MUST NOT rate it.
- **FR-013**: Each score MUST belong to the set 0, 0.5, 1, 1.5, 2, 2.5, 3, 3.5, 4, 4.5, 5.
  Values outside this set cannot be saved or silently converted.
- **FR-014**: The system MUST distinguish unrated occurrences from occurrences rated zero;
  both situations must be recognizable in the interface.
- **FR-015**: The system MUST calculate each reviewer's playlist score as the arithmetic
  mean of that reviewer's rated occurrences, each with equal weight; unrated occurrences
  count neither as zero nor toward that reviewer's divisor.
- **FR-016**: Each reviewer's average MUST appear from their first confirmed rating,
  marked as partial while their rateable occurrences remain unrated, with their own
  rated/total progress.
- **FR-017**: The overall average MUST be displayed with up to two decimal places,
  rounding to the nearest hundredth with ties rounded up; 0.125 becomes 0.13. Calculation
  uses original scores without intermediate rounding, and the average is not limited
  to 0.5 increments.
- **FR-018**: A reviewer's evaluation MUST be complete when that reviewer has rated all
  rateable occurrences, regardless of other reviewers' progress. With none of their
  occurrences rated, no average may be shown for that reviewer.
- **FR-019**: The system MUST allow an optional text comment alongside an occurrence's score,
  written by its reviewer. Direct-share comments are visible only to that reviewer and
  the sender; group-share comments are visible to authorized members of the target group.
- **FR-020**: Track highlights MUST be recorded in these free-text comments; there is
  no requirement for tags, overall playlist comments, or participant conversations.
- **FR-021**: Confirmed submissions, rating and comment changes including edits, and group-membership changes MUST update
  affected authorized feeds and open views within five seconds, with an active
  connection, an available service, and operation within quotas.
- **FR-022**: Scores, comments, the average, and progress MUST correspond to the same set
  of confirmed information; the interface cannot announce success for an unconfirmed write.
- **FR-023**: The system MUST indicate connection loss and retrieve current state after
  reconnection before declaring synchronization complete.
- **FR-024**: Retries and duplicate or out-of-order updates MUST NOT duplicate records,
  count a score twice, or silently regress confirmed state. An edit based on outdated
  state of the same track occurrence MUST be rejected with a visible conflict message
  and the latest confirmed values. The persisted feedback MUST remain unchanged until
  the reviewer explicitly resubmits from current state.
- **FR-025**: The system MUST preserve confirmed submission and rating history across sessions,
  including authorship, imported lists, per-occurrence scores, comments, averages, and progress,
  exposing each record only to participants authorized for that private share and review.
- **FR-026**: The system MUST present understandable empty states for no playlists and no ratings.
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
- **FR-032**: The system MUST reject sharing a playlist with no rateable track occurrences
  and explain why, without creating a completed submission in the feed.
- **FR-033**: Each imported occurrence MUST retain its identity within the preserved list;
  repeated tracks receive separate scores and comments and count separately in the average.
  Retrying the same operation cannot create a new artificial occurrence.
- **FR-034**: Progress MUST be calculated separately for each reviewer as their confirmed
  occurrence ratings over the preserved list's rateable total. Equality with a total
  greater than zero marks only that reviewer's evaluation complete.
- **FR-035**: Account activation MUST require a valid, unused, unexpired code from a private
  invitation. Activation and code consumption MUST succeed or fail together. One code MUST
  activate at most one account, including under concurrent attempts; failed activation MUST
  leave the account inactive and the code unused. Missing, invalid, expired, and consumed
  codes MUST be rejected.
- **FR-036**: Membership MUST NOT impose a two-participant business limit. External provider
  quotas and operational capacity constraints MUST be identified separately from product
  membership rules and communicated without misrepresenting invitation-only eligibility.
  The initial automatic Spotify import pilot MUST operate within the provider's allowance
  of up to five allowlisted Spotify accounts. Refused imports MUST explain the limitation
  without publishing an incomplete submission as completed.
- **FR-037**: Account-activation invitation issuance MUST be restricted to the designated
  application owner; ordinary participants, inactive accounts, and unauthenticated people
  cannot issue account-activation invitations. This does not define group admission permissions.
- **FR-038**: Issuing an account-activation invitation MUST require the owner to specify the
  intended recipient's email and MUST produce an unguessable random code bound to that email,
  usable directly or through a private invitation link. The owner MUST be able to share it
  manually; an automated invitation delivery service is not required for the MVP.
- **FR-039**: Account-invitation recipients MUST authenticate, have a verified account email
  matching the invitation's intended recipient email, and explicitly request activation
  before redemption. Different-account or unverified-email attempts MUST NOT activate an
  account or consume the invitation. Opening, previewing, or refreshing its link MUST NOT consume it.
- **FR-040**: After activation, subsequent sign-ins MUST use ordinary authentication without
  requiring a new account-activation invitation or using the activation code as a reusable password.
- **FR-041**: Account-activation invitations MUST expire 24 elapsed hours after issuance.
  Activation at or after that deadline MUST be rejected without activating an account or
  consuming the code. Opening or previewing an invitation MUST NOT extend its lifetime.
- **FR-042**: Activation attempts MUST be subject to a finite, enforced attempt limit and
  restricted retry period. Their values MUST be documented during planning; denied attempts
  MUST NOT activate accounts or consume a still-valid invitation.
- **FR-043**: Persisted account-invitation verification information MUST NOT disclose the original
  usable code. Raw codes MUST NOT be retained in application logs or exposed to other
  participants; issuance and verification MUST protect these secrets.
- **FR-044**: An activated participant MUST be able to follow and unfollow another activated
  participant. Following is directional: A following B adds B to A's sharing list without
  requiring B to follow A. A repeated follow MUST NOT create duplicate entries. Unfollowing
  MUST remove B from eligibility for A's new direct shares until A follows B again, without
  changing existing direct-share permissions or reviews, B's reverse follow, or group memberships.
- **FR-045**: A sender MUST choose a sharing context: a direct share to one or more selected
  people they follow, or a share with a previously created private group they are authorized
  to use. Neither mode makes the playlist public to other application members.
- **FR-046**: A direct playlist share MUST be accessible only to its sender and selected
  recipients; each review within it is restricted to its sender-reviewer pair.
  A group share MUST restrict playlist and review access to its authorized group audience.
- **FR-047**: Account activation, following, being followed, a connection with a recipient,
  or possession of a forwarded link MUST NOT grant access by themselves. Access requires
  an applicable direct-share permission or authorization within the target private group.
- **FR-048**: Feeds, history, direct access, and live updates MUST enforce the sharing-mode
  permissions for playlist data and for each review, including comments and derived summaries.
- **FR-049**: When creating a direct share, the system MUST reject a new direct recipient
  the sender does not follow, without silently expanding the authorized audience.
  Group recipient eligibility is governed by the target group's membership rules.
- **FR-050**: Activated participants MUST be able to find other activated participants by
  username through an in-app search menu and follow the selected account from the results.
  The selected account's identity MUST determine the resulting follow.
- **FR-051**: Member search and following lists MUST expose only the participant identification
  needed for discovery and selection; they MUST NOT expose private playlists, ratings,
  comments, account activation codes, or credentials.
- **FR-052**: In a direct share, only the sender and an individual reviewer MUST be able to
  read that reviewer's scores, comments, average, and progress. Selecting multiple people
  for the same playlist MUST NOT disclose their reviews to one another.
- **FR-053**: Activated participants MUST be able to create private groups. Every current
  group member MUST be allowed to share an eligible playlist with that previously created
  group, including members who are not its creator. Pending invitees, removed members, and
  other non-members MUST NOT post to it. The group MUST retain its creator identity,
  separately from the application's invitation-issuing owner role.
- **FR-054**: Every current group member MUST be able to read the group's complete playlist
  and review history, including scores, comments, averages, and progress from before they
  joined. Participants outside the current group audience MUST NOT receive that data.
- **FR-055**: The system MUST authorize only the group creator to manage that group's
  membership, including issuing group invitations and removing members. Ordinary group
  membership MUST NOT grant membership administration. The application-owner role alone
  MUST NOT grant group administration, posting permission, or access to private group content.
- **FR-056**: Reviews MUST be scoped to their sharing context. Sharing the same Spotify
  playlist directly and with a group MUST NOT copy or expose private direct-share feedback
  in the group context.
- **FR-057**: Each reviewer's scores and comments MUST remain associated with that reviewer.
  Permission to read another group member's review MUST NOT permit writing on their behalf.
- **FR-058**: A group creator MUST invite activated participants they follow. Each group
  invitation MUST identify its target account, and only that authenticated account may
  accept it to become a member; invitation issuance alone MUST NOT add the member.
- **FR-059**: A pending group invitation MUST NOT grant access to group playlists or reviews.
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

- **FR-063**: An eligible reviewer MUST be able to edit their own saved track scores and
  comments while authorized for the sharing context, including after completing a review.
  Confirmed score replacements MUST recalculate that reviewer's average from current
  scores without adding a rating or changing rated/total progress or completion status.
  Comment-only edits MUST NOT change scores, average, or progress. Invalid or unauthorized
  edits MUST leave confirmed state unchanged; reading another review MUST NOT permit editing it.

**Permission matrix:**

| Resource or action | Authorized actor and conditions |
| --- | --- |
| Issue an account-activation invitation | Designated application owner; specify the intended recipient email. |
| Redeem an account-activation invitation | Signed-in account with the matching verified email, a valid unused code, explicit activation, and less than 24 hours elapsed since issuance. |
| Search for members; follow or unfollow | Activated participant; manage only their own directional follows. Search exposes identification, not private content. |
| Create a direct playlist share | Activated sender with authorized Spotify import; select people the sender currently follows. |
| Read a direct playlist share | Its sender and selected recipients; unfollowing does not revoke an existing share. |
| Read a direct review, average, or progress | Only the sender and that individual reviewer; co-recipients cannot read each other's feedback. |
| Create a private group | Activated participant, recorded as its creator. |
| Invite or remove group members | Only that group's creator; new invitees must be activated accounts the creator follows. |
| Accept a group invitation | Only the authenticated invited account; acceptance establishes membership and does not activate an application account. |
| Post a playlist to a group | Any current member with an eligible Spotify import; pending invitees and removed members cannot post. |
| Read group playlists and reviews | Current group members, including history from before joining; removed members lose all group access. |
| Create or edit a rating or comment | Its eligible reviewer: a selected direct recipient or current group member, excluding the playlist sender. Completion does not lock editing; another person's feedback cannot be changed. |
| Read history or receive live updates | Apply the same sharing-context and review permissions as the corresponding reads. |
| Access private group content as application owner | Application ownership grants no override; current group membership is required. |

### Quality and Governance Constraints

- **QC-001**: The mobile experience MUST meet the constitution's criteria: a minimum width
  of 320 px without horizontal scrolling in critical flows, text enlarged to 200%,
  touch targets of at least 44 by 44 px, and its specified minimum contrast ratios.
- **QC-002**: Verification MUST cover access, isolation, complete import, scores, average
  calculation, comments, history, synchronization, account activation, invitation issuance,
  group admission, membership administration, authorized edits, conflicts, and failure recovery.
- **QC-003**: MVP infrastructure and incremental project spending MUST remain zero.
  Constitution v3.0.0 permits only the application owner's already-held personal Spotify
  Premium subscription as an external prerequisite; it does not permit a project-funded
  subscription, upgrade, paid API access, or any other paid dependency. DP-01 is resolved.
- **QC-004**: Principles I, II, VI, VIII, and X remain binding by reference to the constitution
  without introducing implementation choices into this functional specification.
- **QC-005**: History of first-party ratings and retention of imported information MUST
  respect Spotify's conditions; data maintenance and removal requirements must be checked
  during planning without promising unlimited retention of external data.

### Scope Boundaries

**Included in the MVP:** owner-issued private invitation codes or links, verified-email
recipient binding, explicit single-use activation within 24 hours of issuance, normal
subsequent authentication, and no fixed two-participant business limit. Participants can
search by username, follow and unfollow, share privately with selected followed people,
create private groups, accept group invitations, and share playlists as current group
members; the creator manages membership. The MVP includes authorized import of owned or
collaborative Spotify playlists, private feeds with authorship, per-track ratings and
comments editable by their reviewer, progressive averages, automatic updates, history,
and home screen installation.

**Outside the MVP:** audio playback or downloads, a public social network, monetization,
push notifications, tag classification, integration with other music services, overall
playlist comments, participant chat, a public community feed, automatic visibility to all
application members, and manual track entry or tracklist file uploads. User-supplied
tracklists are a possible future expansion requiring a separate scope decision.

Spotify read integration and structured per-track ratings are part of the scope approved
in the requester's subsequent responses. The initial exclusion of these two capabilities
no longer applies. The application does not need to modify playlists on Spotify.

### Key Entities *(include if feature involves data)*

- **Participant**: a person with a recognizable identity, a searchable username, an account activation state, and
  relationships to their submissions and ratings. Private access requires account activation
  using a single-use code from a private invitation; membership is not capped at two.
- **Owner**: the designated account authorized to issue invitations. Ordinary participant
  membership does not confer this authority; secure initial owner setup is a planning concern.
- **Private Invitation**: owner-issued account-activation permission to join Mayonnaise, with
  an issuer, intended recipient email, creation time, expiry time 24 hours after creation,
  and its activation code.
  Only a signed-in account with that verified email may redeem it. Delivery is private and
  may be manual; possession of a forwarded code does not change its recipient.
- **Activation Code**: an unguessable secret attached to a private invitation, represented
  in persisted storage only by protected one-way verification data, with unused or consumed
  status and its successful account activation when consumed. An expired code is invalid.
  It cannot activate a second account or serve as a recurring sign-in credential.
- **Spotify Authorization**: permission granted by a participant to read owned or collaborative
  playlists; it may be denied, expire, or be revoked. It does not itself authorize entry
  into the private Mayonnaise space.
- **Follow**: a directional connection from one activated participant to another, identifying
  the follower and followed account. It places the followed account in the follower's
  recipient-selection list and does not require a reciprocal connection. It is separate
  from account activation and does not automatically expose either person's playlists.
  The same ordered pair has only one active follow entry. Unfollowing removes that
  connection and future direct-recipient eligibility until followed again; existing
  direct shares, reviews, the reverse follow, and group memberships remain independent.
- **Private Group**: a previously created group with its own identity, creator, and members.
  Any current member can share an eligible playlist with it; its reviews are visible within
  the authorized membership. Only its creator manages membership, and the creator is not
  necessarily the application's account-invitation owner.
- **Group Invitation**: a pending or accepted invitation issued by the group creator to
  an activated account they follow. Only the intended account can accept it. It does not
  activate an application account or reuse an account-activation code.
- **Group Membership**: an invited participant's accepted membership, managed only by the
  creator, with current or removed status. Current membership grants access to the group's
  entire history regardless of join date. Removed status grants no private group access,
  including to earlier content or the participant's own reviews within that group.
- **Playlist Submission**: a private share by a participant, with a Spotify reference,
  playlist identification, authorship, submission time, imported list, and sharing mode:
  direct with selected recipients, or group with a target private group.
- **Playlist Recipient**: a selected participant from the sender's following list for a
  direct share, or an eligible member of the target group for a group share. Sharing
  mode determines which other participants can read that person's review.
- **Imported Track Occurrence**: an appearance of a track in a submission's list, with its
  original position, name, artist when available, and Spotify reference. Two appearances
  of the same track are different occurrences, even with the same reference.
- **Excluded Evaluation Item**: a source item that cannot be imported as a track, associated
  with the submission and available exclusion reason; it must be reported without entering
  the rateable total.
- **Track Occurrence Rating**: one current score assigned by an eligible reviewer, associated
  with the occurrence, submission, reviewer, and latest confirmation time, with an optional
  comment. Authorized edits replace that reviewer's current values without creating
  another occurrence or another rating for the same occurrence.
- **Playlist Evaluation Summary**: one reviewer's result within a particular sharing
  context, including their rated occurrences, rateable total, average, and unrated,
  partial, or complete status. It is derived from that reviewer's track scores, not an
  independently entered manual score.

## Success Criteria *(mandatory)*

### Measurable Outcomes

The following volumes and times are acceptance targets for this specification, not completed
measurements or maximum usage limits. Tests use at least three activated participants
and fictitious data or authorized test playlists. Network and availability conditions
must be recorded; fixture counts are not product membership caps.

- **SC-001**: All activated test participants can access the space and recognize submission
  authorship; 100% of anonymous access, inactive-account, unauthorized-person, and
  ended-session scenarios reveal no private data.
- **SC-002**: In an observed test, each participant can share a compatible playlist without
  verbal help within 60 seconds, starting with a copied link and previously granted
  external authorization and pilot allowlisting, when the external service is available.
- **SC-003**: For a test playlist with 60 supported tracks, 100% are represented after import,
  even when received in multiple parts, and no partial list is presented as complete.
- **SC-004**: The first confirmed rating produces a partial average; for scores of 2.5 and 3,
  participants see 2.75, and adding 5 produces an average of 3.5 and completion of three
  of three. The repetition and rounding examples in Story 3 also produce their exact
  expected results. Zero, no rating, and invalid values are distinguished in 100% of cases.
- **SC-005**: Across ten confirmed changes alternating between three connected test sessions,
  100% appear in every other authorized test session within five seconds without manual reloading,
  with the service available, within quotas, and under documented network conditions.
- **SC-006**: After ending and reopening sessions, all authorized test participants retrieve 100% of records
  in a test set of ten submissions, known scores, and comments, preserving averages,
  progress, and the imported list.
- **SC-007**: All eligible test reviewers can rate three tracks, write a comment, and revisit the result
  without verbal help; a score without a comment is also accepted.
- **SC-008**: Critical access, submission, rating, and history flows can be completed on iOS
  and Android, including after home screen installation, without losing essential content
  or actions at a width of 320 px and with text enlarged to 200%.
- **SC-009**: In every connection-loss and recovery scenario, no unconfirmed write appears
  completed, and retrying does not create duplicate ratings or submissions.
- **SC-010**: Changing the tracks of a previously submitted playlist on Spotify changes zero
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
- **SC-015**: Across account-invitation issuance and preview tests, 100% of owner-authorized creations
  produce a new usable invitation, 100% of non-owner issuance attempts are denied, and
  opening or previewing an unused link causes zero activations and zero code consumption.
- **SC-016**: Every tested failed sign-in or failed activation leaves the account inactive
  and the invitation unused. An otherwise valid invitation is accepted before its 24-hour
  deadline; 100% of attempts at or after that deadline are rejected with zero activations
  and zero code consumption. Previewing the link changes its expiry by zero seconds.
  All subsequent sign-ins to an activated account require zero additional invitations.
- **SC-017**: In all configured attempt-limit tests, further attempts during the restricted
  period are denied without activation or code consumption. Inspection of persisted
  invitation verification data and logs reveals zero original usable activation codes.
- **SC-018**: Successful direct-sharing tests cover both one selected followed participant and
  at least two selected followed participants; 100% can access the playlist and submit an
  eligible review, while the sender can access the submission.
- **SC-019**: Across feed, history, direct-link, and live-update tests, 100% of attempts by
  activated participants outside the direct or group audience, including non-selected followed
  people and followers of recipients, reveal zero private data from the share.
  Forwarding a link grants zero additional authorizations.
- **SC-020**: In all member-discovery test cases, searching a known username identifies
  the intended activated account. Following it from the results adds exactly one entry
  to the follower's sharing list without a reciprocal follow. Search and follow-list
  inspection expose zero private playlist, review, activation-code, or credential data.
- **SC-021**: In all direct-share tests with multiple recipients, the sender can read each
  review and each recipient can read only their own review; attempts to read another
  direct recipient's scores, comments, average, or progress disclose zero private review data.
- **SC-022**: Group-share tests include successful submissions by the creator and a current
  non-creator member. Every tested posting attempt by a pending invitee, removed member,
  or other non-member creates zero group submissions. All authorized current group members
  can read every review for the share, including its author, average, and progress, while
  non-members receive zero private review data.
- **SC-023**: Tests that share the same Spotify playlist through direct and group contexts
  reveal zero direct-review records through group access. All attempts to write on
  another reviewer's behalf are rejected.
- **SC-024**: In all group-administration tests, only the creator can invite or remove
  members; non-creator members, outsiders, and an application owner without the creator
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
- **SC-030**: In every import test with a Spotify account outside the pilot allowlist,
  the participant receives an explanation and zero completed submissions are created.
  Account activation alone is never treated as Spotify API authorization.
- **SC-031**: In all unfollow tests, the unfollowed person is absent from the follower's
  recipient list for new direct shares and cannot be selected until followed again.
  Unfollowing changes zero existing direct-share permissions, saved reviews, reverse
  follows, or private group memberships.
- **SC-032**: Edit tests cover partial and completed reviews. Replacing 2 with 5 in a
  completed two-track review with scores of 2 and 4 produces 4.5, two of two rated, and
  complete status; a comment-only edit changes zero scores or summary values. All invalid,
  unauthorized, and stale conflicting edits leave confirmed state unchanged. Confirmed
  edits persist across sessions and reach authorized viewers within the five-second
  conditions of SC-005, without exposing feedback to unauthorized viewers.

## Assumptions

### Confirmed Product Decisions

- **D-001**: Membership is not limited to two participants; private invitations and single-use
  account activation codes are required.
- **D-002**: MVP playlists come from Spotify; an integration will import their tracks.
- **D-003**: Each eligible reviewer rates track occurrences; their playlist score is the
  arithmetic mean of their rated occurrences, displayed with up to two decimal places.
- **D-004**: The average is partial from the first rating and becomes complete when all
  rateable tracks are rated; displaying a result does not require waiting for the last score.
- **D-005**: Tags were removed from the product. Comments and highlights are optional text
  alongside each track's score, without a separate conversation or overall playlist comment.
- **D-006**: The requester already holds personal Spotify Premium. The owner-requested
  exception in constitution v3.0.0 permits using the application owner's existing
  subscription without incremental project expense; infrastructure remains free.
- **D-007**: Only playlists that an authorized participant owns or collaborates on will
  be shared; importing third-party playlists outside these conditions is not promised.
- **D-008**: The list used for evaluation is the one imported at submission time and remains
  preserved even when the original playlist changes on Spotify.
- **D-009**: Track occurrence scores use 0.5 increments from 0 to 5; the overall average
  uses up to two decimal places, rounded to the nearest hundredth with ties rounded up.
- **D-010**: Repeated tracks receive one score per occurrence; all rated occurrences
  enter the average with equal weight.
- **D-011**: Only importable tracks are rated, with exclusions explicitly reported;
  playlists without any rateable occurrences are rejected.
- **D-012**: Only the designated application owner issues account-activation invitations, shared manually
  as a private code or link; automated message delivery is not required.
- **D-013**: Invitation redemption follows authentication and an explicit activation action.
  Opening a link does not redeem it; activation and consumption succeed together, and
  later sign-ins do not require another invitation.
- **D-014**: Codes are unguessable, expire, are stored only as protected one-way verification
  information, and are protected against guessing attempts. Each invitation is bound to
  the intended recipient email specified by the owner and can be redeemed only by a
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
- **D-018**: Direct-share feedback is private between the sender and each reviewer, even
  when the same playlist is directly sent to multiple selected people.
- **D-019**: Participants can create private groups. Any current member can share an eligible
  playlist with a group created beforehand, and authorized group members can see its reviews.
  Each reviewer retains their own scores, comments, average, and progress; a combined group
  average has not been requested or defined.
- **D-020**: Only a private group's creator manages its membership. The creator invites
  people they follow, and each person must accept before joining.
- **D-021**: Group invitations target existing activated accounts and are separate from
  account-activation invitations and codes; a pending group invitation grants no group access.
- **D-022**: Current group members see the entire group history. Removed members lose group
  access, including to earlier content and their authored group reviews; independent
  direct shares remain separate, and group review history is preserved for current members.
- **D-023**: The initial automatic Spotify import pilot uses up to five allowlisted Spotify
  accounts. This provider allowance is separate from Mayonnaise membership rules.
  Future expansion may use Spotify-approved access or a separately approved user-supplied
  tracklist feature; neither approval nor a fallback is assumed available for the MVP.
- **D-024**: Eligible reviewers can edit their own saved track scores and comments at any
  time, including after completing a review, while authorized for the sharing context.
  Score edits update the average without adding ratings or changing progress; comment-only
  edits do not affect scores or summaries. Completion does not lock a review.

### Dependencies and Verified Constraints

Spotify documentation and invitation security guidance were verified on 2026-11-19;
external conditions must be rechecked during planning.

- Development mode requires the registered Spotify API application's owner to have active
  Premium and allows up to five authenticated, allowlisted Spotify users for a new app.
  The Premium requirement concerns that developer account; it does not grant access to
  participants' playlists. Each sender grants read authorization through their own Spotify
  account. This external allowance is separate from Mayonnaise membership, and five
  allowlisted accounts are the approved initial import pilot boundary. The existing personal
  Premium prerequisite is permitted by constitution v3.0.0 without additional project
  spending; the exception does not change Spotify eligibility or quotas.
  [Spotify — Quota modes](https://developer.spotify.com/documentation/web-api/concepts/quota-modes).
- Extended Quota Mode removes the development-mode allowlist but requires Spotify approval.
  Published eligibility includes a registered organization, a launched service, and at least
  250,000 monthly active users, alongside other criteria. Approval is not a committed growth
  dependency for this MVP.
  [Spotify — Extended quota eligibility](https://developer.spotify.com/documentation/web-api/concepts/quota-modes).
  Spotify permits one client ID per application; multiple registrations are not an approved
  way to expand this pilot's allowance.
  [Spotify — Access, usage, and quotas](https://developer.spotify.com/policy#vii-access-usage-and-quotas).
- Reading tracks requires the connected user to own or collaborate on the playlist;
  access to a link does not guarantee this capability. The documentation also requires
  source attribution for displayed content.
  [Spotify — Get Playlist Items](https://developer.spotify.com/documentation/web-api/reference/get-playlists-items).
- Private Mayonnaise access and Spotify read authorization are distinct needs. The
  authentication and email verification mechanisms, secure owner bootstrap, activation
  attempt-limit values, and integration solution will be defined in the plan.
- Invitation security research adapts the random, confidential, expiring, single-use token
  pattern and guessing protections described in
  [OWASP's one-time token guidance](https://cheatsheetseries.owasp.org/cheatsheets/Forgot_Password_Cheat_Sheet.html).
  Concrete generation, verification, and indivisible redemption mechanisms belong in planning.

### Pending Decisions

**DP-06 — Expansion beyond the Spotify import pilot.** Before broader automatic import access,
recheck Spotify eligibility and obtain the required approval. If approved access is
unavailable, decide separately whether to add user-supplied tracklists through manual
entry or file upload. The initial pilot does not promise either expansion path, impose
a permanent five-member product limit, or assume that all possible recipient flows meet
provider conditions without verification during planning.

**Policies not introduced in this version:** deleting ratings or submissions, editing
playlist submissions, intentionally resubmitting the same link, specific feed ordering,
comment length limits, and score version history were not defined by the requester.
They require a separate scope decision if added; this specification establishes neither
approval nor a permanent prohibition. History shows current saved submissions and reviews,
including confirmed feedback edits; a prior-version audit view is not required.
