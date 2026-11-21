# Spotify Integration Contract

**Date**: 2026-11-20 | **Status**: Current Phase 1 design.
**Authority**: [spec.md](../spec.md), [import-feasibility.md](../import-feasibility.md),
[research.md](../research.md), constitution v4.0.0.

## Approved source matrix

The requester confirmed no existing Spotify app and explicitly approved the following MVP.

| Actor/source | Authentication | Captured scope |
| --- | --- | --- |
| VIP submits own/collaborative playlist | That actor's authorized personal connection, provider allowlist and owner/collaborator eligibility | Full declared playlist, every page and stable playlist snapshot. |
| Normal submits playlist | None permitted in this MVP | Denied; no share or borrowed connection. |
| Normal or VIP submits album | Server-held application Client Credentials token, explicit market | First100 source positions maximum, before exclusions; disclose actual cutoff. |

Administrator role alone does not grant VIP or content access. Normal can receive/review
authorized playlist shares and participate in results/rankings. No user-supplied file/
manual editor, Embed scraper, anonymous Web Player token, shared personal integration
account, extra client ID or paid extraction is included.

Use one new Development Mode application, initially at most five authorized personal
Spotify accounts. This is an external connected-path allowance, not Mayonnaise membership
capacity. The already-held developer-owner Premium prerequisite adds zero project spending;
losing it makes affected integration unavailable, never triggers purchase/upgrade.
Catalog requests use application credentials but still depend on the application's real
eligibility, quotas and current provider conditions. No production access is already set up.

## Link, URL and external-data boundary

Accept supported HTTPS open.spotify.com playlist/album links and extract the expected
kind and provider ID. Reject source-kind mismatch and unsafe schemes/hosts. Query/tracking
parameters do not change identity. If short links are supported, follow only bounded
validated Spotify redirects with a fixed allowlist; do not create a generic URL fetcher.
Construct Web API endpoint URLs server-side; never follow client-supplied next URLs or
fetch arbitrary artwork URLs server-side without validating provider origin/protocol.
Reject userinfo, IP/internal hosts and unsupported resource kinds. No credential input
from another participant.

Decode every provider response from unknown. Minimum eligible track data establishes
stable provider identity; unsupported episodes, local/unavailable/unidentified items
become exclusions. Preserve returned original position and duplicates before evaluation
positions are assigned. Missing optional title/cover/artist/duration data stays absent
with the shared fallback, not an invented value.

Staged import_items.exclusion_reason uses exactly the canonical set and NULL/eligibility
rules in [data-model.md](../data-model.md#privateimport_items). Eligible items have a NULL
reason; ineligible items require episode, local_item, unavailable, unidentified or
unsupported. Validate these pairs at decoding/staging and in SQL, then preserve the
reason unchanged at publication. Unknown codes or inconsistent pairs are invalid stage
data, not a new exclusion category or permission to publish incomplete source coverage.

## Personal connection and PKCE

Use S256 authorization-code PKCE and a cryptographically random one-use state whose
digest, encrypted verifier, initiating native Auth session, actor authority version,
connection/generation and ten-minute expiry are private. Validate them and current VIP
authority on start and callback. Bind the callback to the initiating account/session;
a later account switch cannot link another user. Late callback after downgrade,
disconnect or generation change fails without restoring data.

Use minimal playlist-read-private and the supported user-read-private profile scope for
the provider account ID; do not request user-read-email as Mayonnaise email proof.
Get current provider identity server-side. OAuth never activates a Mayonnaise account.

Allowed callbacks are exact:
`http://127.0.0.1:3000/api/spotify/callback` locally and the configured stable HTTPS
production origin plus that path. Do not use localhost aliases, HTTP non-loopback,
wildcards or arbitrary preview origins. Validate return paths locally. Redact provider
codes/state/tokens from application logs and never cache callback requests/responses.

Encrypt token envelopes with authenticated Node crypto and an environment-managed key
and key ID. Tokens are never browser DTOs or logs. Handle actual expires_in; approximate
one-hour access lifetime is not a hardcoded promise. Developer refresh grants need
reauthorization at the documented lifetime (currently six months); refreshing an access
token must not be assumed to extend that grant. A short refresh lease plus generation/
authority CAS prevents races. Perform network exchange outside DB locks, then recheck
current state before persisting. Rejected/expired grants produce reauthorization guidance.

## Connected playlist import

1. Verify live actor, active VIP and own connected grant. Read playlist metadata and
   owner/collaborator eligibility through the official API; a URL alone proves neither.
2. Capture initial snapshot and original source-total evidence. Page playlist items using
   the current /v1/playlists/{id}/items contract, limit at most50. Process at most two
   item pages per continuation and persist next source offset.
3. Preserve all original positions, including unsupported entries. Stage minimal validated
   metadata/eligibility, not arbitrary provider response archives.
4. Read the final playlist snapshot. If changed, discard inconsistent stage items and
   restart at most twice; further change reports an understandable failure/incomplete
   state. Never publish a stitched full list.
5. Attest complete source coverage and stage actor/session/connection/generation/authority/
   audience, positive eligible count and 24-hour expiry. Final publish runs under the
   user JWT/RLS transaction, rechecking all current permissions.

No completed share appears while collecting, failed or insufficiently proven. If all
occurrences are excluded, return the reasons and create no completed collection.
Retries replace the same staged positions and publication returns one context-specific
collection per operation. Never hold DB transactions across provider network activity.

## Catalog album import

Use official server-to-server Client Credentials with protected application client ID/
secret. No personal Spotify account, grant generation or participant OAuth is created.
Keep access tokens in protected server process memory for their actual valid lifetime;
renew without persisting participant-linked credentials.

Use individual /v1/albums/{id} and /v1/albums/{id}/tracks endpoints; do not rely on removed
batch album endpoints. Configure an explicit ISO3166-1 alpha-2 market. Read metadata:
name, artists, available images, release information and total_tracks; then page ordered
tracks, maximum50 per request. Preserve disc_number, track_number, returned order and
duplicated occurrences. Album catalog resources may represent singles/compilations under
A-09; do not invent a separate ranking or exclusion.

Requested scope is first_100: capture source positions 1..min(100,established original
scope) before exclusions, never later entries to fill100 rateable tracks. Establish
coverage for all requested positions. A source with 120 positions yields 100 captured,
known20 omitted and an actual cutoff label. With60 fully established positions, its
published scope may be full while source_mode remains catalog_album. Unknown original
total remains unknown, and first 100 coverage cannot be claimed from a shorter/unproven
response. Persist captured/rateable counts plus purgeable original total/continuation
evidence.

Albums have no documented snapshot_id. Record capture time and observed frozen set;
detect inconsistent page/identity/count responses and fail/retry without promising an
atomic provider snapshot. No personal playlist-owner requirement applies to albums.
Published occurrence identities/order are frozen against later changes, subject to
separate provider-data retention. Actual app access, multi-disc ordering, >100 scope,
market omissions and metadata need integration acceptance.

## Errors, continuation and latency

Handle invalid resource,401/revoked authorization,403permission,404/unavailable,429with
Retry-After and transient service/network failure. Retry only within a bounded request
budget; return a safe retry time/incomplete stage when it exceeds the budget. No hidden
background worker, paid queue or unbounded retry. Vercel action requests use a bounded
duration (planned60s, within Hobby limits); source continuation preserves progress.

A ready stage is not publication. User-JWT publication validates current audience and
source attestation after provider work; VIP downgrade, session loss, unfollowed new direct
recipient or group removal denies publication. Valid catalog source does not depend on
any participant's personal connection, but active actor/session still matters.
The60-second sharing criterion is measured under documented source/network/quota
conditions, not guaranteed through throttling/outage.

## Metadata display and retention

Provider metadata identifies Spotify and links back to the relevant resource. Show actual
cover art without cropping, overlays or alteration; use a separate layout region for
ranking badges/stars. Missing art uses the shared first-party placeholder. Cache private
DTOs/provider response data neither in the service worker nor a shared Next/CDN cache.

Separate immutable internal occurrence IDs/order from purgeable provider IDs, source
URLs, titles, artwork references, artist fields, snapshots, totals and staging. Metadata
freshness is at most24hours before revalidation or hiding. Never refresh a frozen
occurrence by matching a changed playlist's current position/title.

Personal disconnect exclusively locks its connection, advances generation and invalidates
OAuth/import/refresh/metadata writes before applicable connected data purge, normally
immediate and within five days including controlled copies. IDs/URLs are not permanently
exempt. Independently obtained catalog albums are not erased merely because one personal
grant is disconnected; they have their own revocation/freshness/erasure scope. Do not
relabel connected origin as catalog to retain it.

History/first-party review retention remains conditional under QC-005. Assess remaining
provider associations during erasure; an internal placeholder does not automatically make
feedback anonymous. Paused Free projects may not run cleanup jobs; operator procedures
must cover unavailable erasure requests/restoration.

## Acceptance evidence required before delivery

Use actual eligible app credentials in a protected integration environment, never CI PR
fixtures. Verify own/collaborator and unauthorized playlists, Normal endpoint denial,
five-account allowance behavior, personal reauthorization/downgrade/disconnect races,
all playlist pages/snapshot changes, album Client Credentials access/market/pagination/
multi-disc/order/duplicates/first 100/exclusions, returned artwork and missing metadata.
Inspect source-specific purge and controlled exports. No live Spotify request was executed
by planning. The reviewed documents establish the architecture, not provider endorsement
of a finished application.

Primary sources:
[playlist items](https://developer.spotify.com/documentation/web-api/reference/get-playlists-items),
[Client Credentials](https://developer.spotify.com/documentation/web-api/tutorials/client-credentials-flow),
[Get Album](https://developer.spotify.com/documentation/web-api/reference/get-an-album),
[Get Album Tracks](https://developer.spotify.com/documentation/web-api/reference/get-an-albums-tracks),
[PKCE](https://developer.spotify.com/documentation/web-api/tutorials/code-pkce-flow),
[redirect URIs](https://developer.spotify.com/documentation/web-api/concepts/redirect_uri),
[quota modes](https://developer.spotify.com/documentation/web-api/concepts/quota-modes),
[2026 migration](https://developer.spotify.com/documentation/web-api/tutorials/february-2026-migration-guide),
[terms](https://developer.spotify.com/terms), [policy](https://developer.spotify.com/policy).
