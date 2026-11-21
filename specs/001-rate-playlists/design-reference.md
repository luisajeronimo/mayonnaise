# Mobile Design Reference

**Verified**: 2026-11-20
**Functional authority**: [spec.md](spec.md) and the [constitution](../../.specify/memory/constitution.md).
**Visual reference**: [Mayonnaise in Figma](https://www.figma.com/design/RGjrk61M6HeoLPkaRBeLxb/mayonnaise?node-id=0-1).
**Theme**: Dark only.
**Layout**: Responsive for mobile; prototype dimensions are proportional references.

## Authority and visual sources

The repository defines behavior, authorization, scope, and acceptance criteria. Use the
mobile Figma prototype for composition, typography, colors, icons, and visual hierarchy.
A feature appearing in a frame does not authorize its implementation. Preserve specified
behavior when a frame conflicts with it, adapting the visual treatment. Accessibility
and privacy also take precedence. All implementation copy and repository-authored
examples remain English under AGENTS.md even when the prototype uses another language.

Use only the Figma file and the explicitly supplied logo/favicon as visual sources.
Other files in the Downloads bundle are not sources for this reference. Previously
extracted local-guide values were withdrawn; the values below were read from Figma.

## Access verification and coverage

Figma MCP access succeeded on 2026-11-20. Page `0:1` returned twelve mobile frames.
The private feed returned design context and an image. Read-only Plugin API inspection
retrieved actual paints/fonts from feed, track rating, and account frames; rating and
account screenshots were also viewed. The file was not changed.

The variable helper did not return usable named definitions. Semantic token names here
are repository mappings to observed node values, not claims that Figma contains variables
with those names. [design/figma-inspection.json](design/figma-inspection.json) records
the scoped evidence without temporary asset URLs or participant credentials.

This verifies current access and focused visual extraction. It does not prove access
will never change, that every frame/state has been reviewed, or that an application has
been implemented or tested against the prototype.

| Surface | Node | Frame size, reference only | Evidence |
| --- | --- | --- | --- |
| S01 · Sign-in and identity | [1:2](https://www.figma.com/design/RGjrk61M6HeoLPkaRBeLxb/mayonnaise?node-id=1-2) | 390 × 884 | Structure inspected |
| S02 · Account activation | [1:70](https://www.figma.com/design/RGjrk61M6HeoLPkaRBeLxb/mayonnaise?node-id=1-70) | 390 × 884 | Structure inspected |
| S03 · Owner access invitations | [1:151](https://www.figma.com/design/RGjrk61M6HeoLPkaRBeLxb/mayonnaise?node-id=1-151) | 390 × 883 | Structure inspected |
| S04 · Private feed | [1:275](https://www.figma.com/design/RGjrk61M6HeoLPkaRBeLxb/mayonnaise?node-id=1-275) | 390 × 1072 | Properties and screenshot inspected |
| S12 · Rate and edit a track | [1:697](https://www.figma.com/design/RGjrk61M6HeoLPkaRBeLxb/mayonnaise?node-id=1-697) | 390 × 755 | Properties and screenshot inspected |
| S11 · Playlist detail | [1:842](https://www.figma.com/design/RGjrk61M6HeoLPkaRBeLxb/mayonnaise?node-id=1-842) | 390 × 1146.75 | Structure inspected |
| S10 · Spotify import | [1:1025](https://www.figma.com/design/RGjrk61M6HeoLPkaRBeLxb/mayonnaise?node-id=1-1025) | 390 × 1056.25 | Structure inspected |
| S09 · Share a playlist | [1:1157](https://www.figma.com/design/RGjrk61M6HeoLPkaRBeLxb/mayonnaise?node-id=1-1157) | 390 × 1066.375 | Structure inspected |
| S06 · People and following | [1:1498](https://www.figma.com/design/RGjrk61M6HeoLPkaRBeLxb/mayonnaise?node-id=1-1498) | 390 × 600 | Structure inspected |
| S08 · Group and administration | [1:1635](https://www.figma.com/design/RGjrk61M6HeoLPkaRBeLxb/mayonnaise?node-id=1-1635) | 390 × 1316 | Structure inspected |
| S07 · Groups and invitations | [1:1846](https://www.figma.com/design/RGjrk61M6HeoLPkaRBeLxb/mayonnaise?node-id=1-1846) | 390 × 740 | Structure inspected |
| S14 · Account and Spotify connection | [1:2113](https://www.figma.com/design/RGjrk61M6HeoLPkaRBeLxb/mayonnaise?node-id=1-2113) | 390 × 1114.75 | Properties and screenshot inspected |

Some screenshots include effects outside frame bounds. Their exported pixel size does
not redefine the frame or the application's supported viewport.

## Responsive mobile behavior

Preserve visual identity and hierarchy rather than reproducing a rigid canvas. The
390 px frame width is not an application width cap or a fixed device requirement.

- Use fluid containers, responsive padding, wrapping, and intrinsic content sizing.
  Do not transplant canvas coordinates, fixed frame heights, or absolute page placement.
- Support the constitutional 320 CSS px minimum, wider phones, portrait/landscape,
  browser chrome, safe areas, 200% text enlargement, and the on-screen keyboard.
- Let translated English copy, titles, comments, and error messages wrap naturally.
  Do not clip content or shrink touch targets to preserve a reference measurement.
- Share mobile navigation/header layouts. Position navigation for the actual viewport
  and safe area rather than copying the different canvas offsets between frames.
- Keep primary actions and rating controls reachable. Preserve unsaved-edit, privacy,
  and lifecycle rules when the keyboard or available height changes.
- Validate representative 320, 390, and 430 CSS px widths, orientation changes, enlarged
  text, and actual iOS Safari/Android Chrome. Fixed screenshots alone do not establish
  responsiveness.

## Dark-only appearance

Set the root color scheme to dark and use the same dark tokens for public, private,
loading, error, offline, and installation surfaces. Do not add a theme selector, light
palette, or system-driven light appearance. Verify both operating-system preferences.
White is a foreground color within the dark theme.

## Palette read from Figma

[design/tokens.css](design/tokens.css) records the shared reference palette and font roles
for the planned `src/app/globals.css`. It is not an implemented application stylesheet.
Coverage is the three inspected frames, not an exhaustive audit of every canvas state.

| Role | Observed value | Source node |
| --- | --- | --- |
| Canvas | `#121212` | `1:275` |
| Surface/card | `#252525` | `1:298` |
| Inset surface | `#1c1b1b` | `1:320` |
| Raised/secondary surface | `#2a2a2a` | `1:405` |
| Subtle neutral | `#393939` | `1:376` |
| Primary text | `#ffffff` | `1:313` |
| Muted text | `#b8b8b8` | `1:323` |
| Primary pink | `#ffade5` | `1:291` |
| Dark text/icon on pink | `#121212` / `#5e0051` | `1:295` / `1:2316` |
| Accent yellow | `#eaea32` | `1:316` |
| Dark text on yellow | `#323200` | `1:318` |
| Tonal panel | `#231a20` | `1:365` |
| Tonal supporting text | `#d7c0cd` | `1:364` |
| Success | `#7dff95` | `1:2122` |
| Destructive/error accent | `#ffb4ab` | `1:2192` |
| Secondary pink action treatment | `#ffade5` at 10% | `1:339` |
| Header overlay | `#121212` at 85% | `1:2311` |
| Feed navigation overlay | `#0e0e0e` at 90% | `1:464` |

Additional observed paints are retained in the evidence JSON. Do not invent warning/info
colors, unseen interaction states, gradients, or palette ramps from unrelated files.
Map roles consistently and verify actual text/control/focus contrast, opacity, and
background combinations under QC-001. A decorative subtle separator is not sufficient
as the sole essential control boundary.

## Typography and interface icons

Observed type families are Space Grotesk for headings and the feed's primary action,
DM Sans for body/control text, and Space Mono for compact metadata, scores, and navigation.
Use these three shared roles. The evidence records actual styles/variation weights;
a variation weight takes precedence over a misleading imported style label.

Reference examples include 20 px playlist titles, 14–16 px body/control text, and compact
10–12 px metadata. These are observations, not rigid size requirements. Use scalable
text and readable shared variants; retain the constitutional accessibility thresholds.
Do not use tiny metadata styling for essential controls. Use Next.js font facilities
without a runtime font import or an additional typography dependency.

Use Lucide React from the existing plan. Centralize semantic icon mapping, size variants,
stroke treatment, and alignment. Equivalent actions use the same icon and treatment
across screens. Required Spotify attribution assets and the supplied logo are distinct
from interface icons. Do not infer an additional icon library from imported vector layers.

## Shared components, layouts, and prototype inconsistencies

Use `src/components/ui/` for shared primitives, `src/app/(private)/layout.tsx` for the
private shell, and feature modules for behavior. Reuse buttons, fields, alerts, dialogs,
status/progress labels, playlist rows, track occurrence rows, reviewer summaries,
empty/error states, and mobile navigation. Equivalent information must share formatting,
type roles, spacing rules, icon mapping, and interaction states.

The inspected prototype contains differences that should not become application drift:

| Observation | Implementation rule |
| --- | --- |
| Feed canvas `#121212`; account canvas `#131313` | Use the shared canvas token for equivalent page backgrounds. |
| Feed primary action `#ffade5`; rating save `#da66be` | Use a shared primary-button variant, anchored to feed pink with accessible dark text. Treat a distinct action variant as intentional only when its purpose is documented; do not assume the second color is a hover state. |
| One unrated placeholder uses Liberation Mono | Use the shared Space Mono role for equivalent score placeholders; do not introduce a fourth font for this outlier. |
| Navigation icons/treatments differ across inspected screens | Use one shared navigation component and semantic icon map. |
| Some feed actions are only 36 px high | Increase their interactive area to at least 44 × 44 px under QC-001; the prototype's smaller measure is not binding. |
| Rating comment shows an example `48/180` counter | Do not infer a 180-character business limit. Specification and data-model constraints govern validation. |
| Account frame's provider text promises retained internal history | Preserve the repository's qualified retention/cleanup rules; avoid an unconditional promise beyond those contracts. |

Keep private DTOs correctly scoped when sharing presentation components. Reusing a group
reviewer component never grants a direct recipient access to other recipients' feedback.
Keep unrequested tags, playback, public discovery, chat, push, light mode and undefined
edit/delete/admin powers outside scope. The 2026-11-20 specification explicitly adds
administrator invitation authority, Normal/VIP imports, albums, manual general feedback,
collective group results and rankings; extend the recorded visual system for those
approved features rather than treating the old absence as a product prohibition.

## Required surfaces without inspected designs

The frame inventory contains S01–S04, S06–S12, and S14. No dedicated S05 history, S13
review-results, or S15 cross-cutting-state top-level frame was returned on the inspected
page. This is page-inventory evidence, not a claim about every possible Figma page.

Implement required history, authorized review detail/results, and loading/empty/error/
denied/offline/conflict/recovery states using the same shared visual language. Reuse
playlist/reviewer components, the private shell, fields, and alerts. Record the source
frame and adaptation, preserving every repository acceptance and privacy obligation.
Do not invent a new screen style or new behavior to fill a missing design.

## Revised feature surfaces — 2026-11-20

The source frame inventory and visual observations above remain dated evidence, not
proof that the newly requested screens already exist in Figma. The revised spec governs.

- Adapt the S03 invitation form to administrator authority; its source label mentioning
  an owner does not preserve the old sole-owner permission rule.
- Extend account/share surfaces for the approved source eligibility: only VIP submits
  connected owned/collaborative playlists; Normal/VIP import catalog album links.
  Show the album first-100 cutoff and metadata placeholders consistently. Normal users
  retain received-playlist review; no Normal playlist-import control is implied.
- Extend shared review components with distinct track mean/manual general rating and
  favorite/least-liked choices. Do not conflate three separate numeric concepts.
- Add group collective results and separate playlist/album rankings. Keep three visible
  highlighted positions, empty placeholders when needed, numeric grades/accessible stars,
  and compact readable rows for fourth/later places.
- Use the same palette, type roles, Lucide mapping and responsive shell for these absent
  designs; fixed frame measurements remain references rather than device constraints.
- Preserve current direct/group privacy, role/eligibility distinctions, negative states
  and authorization/recovery on the new result/ranking surfaces.

## Supplied brand assets

| Purpose | Repository asset | Original source |
| --- | --- | --- |
| Logo mark | [public/brand/logo.svg](../../public/brand/logo.svg) | `/home/luisajeronimo/Downloads/mayonnaise-stitch-v5/logo.svg` |
| Favicon | [public/favicon.svg](../../public/favicon.svg) | `/home/luisajeronimo/Downloads/mayonnaise-stitch-v5/favicon.svg` |

Preserve these supplied masters without redrawing, recoloring, stretching, or removing the
dot. Both contain identical bytes, SHA-256
`a2f94b3b5dc82f1ef4984c1f64bfe96c6061c2f9c8b8ed361e020b275616bbf6`.
Each is 951,490 bytes with embedded raster images. XML inspection found no script,
foreignObject, or external image reference. These checks do not prove browser favicon
rendering or PWA installation. Derive and verify PNG/apple-touch/PWA icons during
implementation, preserving the approved artwork.

## Acceptance and task-generation obligations

- Build the shared dark palette, type roles, Lucide mapping, controls, and responsive
  shell before composing equivalent feature screens independently.
- Compare relevant implementation views to their Figma references for visual identity,
  then exercise narrower/wider phones, rotation, 200% text, safe areas, and keyboard use.
  Do not make fixed pixel equivalence the acceptance criterion.
- Verify equivalent elements across feed/history/detail and direct/group contexts.
  Record specification/accessibility adaptations and unresolved visual differences.
- Check dark appearance under both OS preferences, contrast, visible focus, touch targets,
  VoiceOver on iOS, and TalkBack on Android with the existing test/device workflow.
- Include required states absent from the prototype while retaining functional tests and
  permission checks. Reuse Vitest/Playwright; no extra UI framework or testing service.
- Reinspect affected Figma nodes if the source changes. This record is a dated reference,
  not a promise that the provider, account permissions, or design will remain unchanged.

Registration of this reference does not implement the application or constitute its
responsive-device, accessibility, or visual-fidelity sign-off.
