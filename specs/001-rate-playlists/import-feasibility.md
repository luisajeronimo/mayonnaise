# Import Feasibility: Approved Spotify Sources

**Checked**: 2026-11-20
**Status**: P0-SOURCE closed by explicit requester-approved scope revision.
**Authority**: [spec.md](spec.md), [research.md](research.md), constitution v4.0.0.

## Approved decision

The requester confirmed there is no Mayonnaise Spotify Developer Dashboard app. After
the limitation was explained, they explicitly approved:

- Only VIP participants submit playlists through their own authorized owner/collaborator
  Spotify connection.
- Normal and VIP participants import catalog albums without a personal Spotify connection.
  This path retains the first-100 source-position limit and explicit cutoff.
- Normal participants may receive and review shared playlists. They do not submit playlists
  in this MVP. Private audiences, group participation and review weights remain unchanged.

This removes the unverified Normal link-only playlist requirement. It does not claim
older app entitlement, changed Spotify rules, public HTML extraction permission or
unlimited provider capacity. No file/manual fallback, paid extractor or borrowed grant
is added. Broader playlist import remains DP-06.

## Source comparison

| Source | Capability and limitation | Decision |
| --- | --- | --- |
| Official oEmbed | Preview title, optional thumbnail, provider/iframe fields; no ordered tracks, source count or pagination contract. | Insufficient for playlist evaluation import. |
| Official iFrame API | Widget controller/playback events; no ordered collection enumeration contract. | No importer selected; playback remains out of scope. |
| Public Embed page/internal data | Earlier samples visibly listed tracks, without complete first-100/order/duplicate/source-total proof; terms also constrain extraction/reuse. | No production HTML scraper/parser. |
| New-app connected playlist Web API | Contents require authenticated owner/collaborator, plus app/provider eligibility. | Selected VIP source, full declared scope and stable playlist snapshot. |
| Official album catalog API | Individual album metadata and paginated album tracks, application Client Credentials and explicit market. | Selected Normal/VIP source, first 100 source positions maximum. |
| Legacy/extended app | Conditions may differ, but the requester confirmed no app exists. | Not applicable to this project's plan. |

Official sources: [oEmbed](https://developer.spotify.com/documentation/embeds/reference/oembed),
[iFrame API](https://developer.spotify.com/documentation/embeds/references/iframe-api),
[playlist items](https://developer.spotify.com/documentation/web-api/reference/get-playlists-items),
[Get Album](https://developer.spotify.com/documentation/web-api/reference/get-an-album),
[Get Album Tracks](https://developer.spotify.com/documentation/web-api/reference/get-an-albums-tracks),
[Client Credentials](https://developer.spotify.com/documentation/web-api/tutorials/client-credentials-flow).

## Earlier public observations and permission finding

The earlier specification review used the web reader (which may use cached responses)
to inspect public examples. It observed oEmbed title/artwork fields without track arrays;
one playlist Embed displayed50 items and one album Embed displayed18. No direct
authenticated project-app source request, complete100-item extraction, original-total
proof, duplicate-preservation validation, playback or provider token was involved.

Samples: [playlist preview](https://open.spotify.com/oembed?url=https%3A%2F%2Fopen.spotify.com%2Fplaylist%2F37i9dQZF1DXcBWIGoYBM5M),
[album preview](https://open.spotify.com/oembed?url=https%3A%2F%2Fopen.spotify.com%2Falbum%2F4aawyAB9vmqN3uQ7FjRGTy),
[playlist Embed](https://open.spotify.com/embed/playlist/37i9dQZF1DXcBWIGoYBM5M),
[album Embed](https://open.spotify.com/embed/album/4aawyAB9vmqN3uQ7FjRGTy).
A later raw-page probe yielded no retained successful parser evidence.

[User Guidelines item5](https://www.spotify.com/us/legal/user-guidelines/) prohibits
scraping/automated information collection. [Widget Terms](https://developer.spotify.com/documentation/embeds/terms)
III.1(a), IV.1(b), IV.1(g)(iii), Local Caching and Widgets Restrictions do not establish
permission to extract internal page data into an independent frozen review database.
That is a provider-contract finding, not a blanket legal judgment or a new requirement
for project-specific written approval.

The [new-app migration guide](https://developer.spotify.com/documentation/web-api/tutorials/february-2026-migration-guide)
and [developer announcement](https://developer.spotify.com/blog/2026-02-06-update-on-developer-access-and-platform-security)
distinguish new rules and postponed endpoint changes for existing apps. No legacy access
can be assumed here because no existing Mayonnaise app exists.

## Artwork and other metadata

The official album response documents name, artists, cover URLs, release information
and total_tracks; track pages include identity, artists, duration and disc/track numbers.
Playlist metadata comes from the actor's eligible official source. Actual fields may be
missing/unavailable: use shared placeholders and accurate labels, not invented covers,
artists or counts. Spotify curator/artist identity never replaces Mayonnaise submitter.

Keep original art unaltered, show source attribution/logo and link back to the resource.
Artwork availability is separate from obtaining all rateable track occurrences.
Preview fields alone neither satisfy track enumeration nor authorize a separate archive.

## Scope, ordering and lifecycle

The first 100 boundary is a product decision for catalog album import, not a universal
Spotify allowance. Request pages up to50, capture source positions before unsupported-
item exclusions, preserve duplicates/disc ordering and report exclusions. A120-position
source yields exactly 100 captured and known20 omitted before exclusions; do not fetch
later positions to fill100 eligible songs. A fully established60-position source may
be marked full. Unknown original total stays unknown, and unproven shorter responses
must not be called complete.

Connected playlists cover all required pages and stable before/after snapshot, with at
most two restarts. Albums have no documented snapshot_id: store observed capture time/
set and reject detected inconsistencies without inventing atomic snapshot guarantees.

Freeze internal occurrence identity/order; keep provider metadata/IDs/URLs/total evidence
purgeable. Connected origin requires its own personal connection/generation; catalog
origin has no personal grant. Personal disconnect purges its applicable connected data,
not independent catalog albums, and may not relabel connected origin to evade erasure.
Freshness/revalidation/hiding and controlled-copy cleanup remain source-specific under
QC-005. See [Spotify contract](contracts/spotify.md).

## Verification still required during implementation

No real Spotify app has been registered or authenticated by planning. Protected integration
acceptance must verify new-app eligibility/Premium/allowlist, Client Credentials album
access and market, page boundaries, multi-disc/order/duplicates/exclusions,120-position
cap, cover/metadata/error states and connected snapshot/revocation/downgrade races.
These test the selected documented routes; there is no remaining unidentified MVP source.

The selected stack and source design are recorded in [plan.md](plan.md); current model/
contracts/quickstart and the current 136-task list are reconciled. Provider access still
requires actual protected integration evidence during implementation.
