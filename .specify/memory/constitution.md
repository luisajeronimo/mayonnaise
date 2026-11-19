<!--
Sync Impact Report — temporary review material; remove before committing.
Version change: 2.0.0 → 3.0.0.
Reason: the owner explicitly requested resolution of the existing Spotify Premium
dependency after confirming the personal subscription is already held.
This is a major version because principle II's blanket prohibition on mandatory paid
plans is relaxed for one named external prerequisite.
Modified principle: II. Zero-Cost Infrastructure → II. Zero-Cost Infrastructure
(title unchanged; one narrow existing-subscription exception added).
Added sections: none.
Removed sections: none.
Other principles, invitation-only membership, and private-sharing obligations: unchanged.
Dependent artifact impact: reconcile the active feature's constitution reference and
DP-01 in the accompanying clarification workflow; templates and commands remain unchanged.
Deferred TODOs: none.
-->

# Mayonnaise Constitution

## Core Principles

This constitution governs Mayonnaise, a private web application where invited participants
share and rate musical playlists. MUST and MUST NOT express compliance obligations. The criteria below apply
to specifications, technical decisions, implementation, review, and operation.

### I. Simplicity First

The project MUST adopt the simplest solution that satisfies the approved requirements.
The MVP MUST remain a single application with internal modules that have clear responsibilities.
It MUST NOT introduce microservices, distributed orchestration, abstractions for hypothetical
needs, or layers without a demonstrated purpose. Managed infrastructure services may be used
when they reduce maintenance and satisfy the other principles. This keeps the effort
proportional to the private MVP's approved scope.

**Compliance criteria:**

- Each new service, layer, or abstraction MUST address an identified requirement.
- The plan MUST justify additional complexity and record the simpler alternative considered.
- A need for microservices in the MVP MUST trigger a constitutional amendment before
  implementation; a justification in the plan alone does not authorize this architecture.
- Review MUST confirm that no components exist solely for hypothetical future expansion.

### II. Zero-Cost Infrastructure

The MVP MUST operate exclusively on free infrastructure resources, including hosting,
database, authentication, synchronization, any storage used, and delivery automation.
Time-limited free trials and promotional credits MUST NOT underpin operation. The project
MUST NOT enable automatic infrastructure billing, paid overages, or mandatory paid plans,
except for the narrowly defined existing Spotify prerequisite below.
This constraint keeps MVP infrastructure and new project spending at zero.

The application's owner may use their already-held personal Spotify Premium subscription
as an external music-service prerequisite. This subscription is separate from MVP
infrastructure and MUST NOT create additional project expense. The exception does not
authorize purchasing a new subscription, upgrading a personal plan, buying API access,
or adding any other paid dependency to operate the MVP. It does not waive Spotify's
access permissions, account eligibility, or quotas.

**Compliance criteria:**

- The plan MUST record each infrastructure service, its free resources, quotas, conditions,
  verification date, and estimated usage by expected activated participants, with official sources.
- The plan MUST separately record the owner's confirmation that the required personal
  Premium subscription already exists, that using it creates zero incremental project
  spending, and that no purchase or upgrade is needed for the proposed MVP.
- Before a delivery changes consumption or infrastructure, review MUST confirm that
  expected operation remains within current free limits and the narrow Premium exception.
- Configuration MUST prevent automatic infrastructure purchases or overage charges;
  infrastructure services that cannot ensure zero cost MUST NOT be adopted.
- If the personal Premium prerequisite becomes unavailable, affected integration features
  MUST explain their unavailability and degrade gracefully. The application MUST NOT
  purchase, renew, or upgrade a subscription with project funds to recover access.
- When an infrastructure quota is reached, the application MUST explain the limitation
  and degrade gracefully; switching to a free alternative or revising scope requires
  a documented decision.
- The exception MUST NOT be applied to hosting, database, authentication, synchronization,
  storage, or delivery automation, and MUST NOT be used to promise unsupported provider access.
- Any paid infrastructure or additional paid-plan exception requires a prior constitutional
  amendment. Existing free-infrastructure and zero-incremental-spending obligations remain binding.

### III. Mobile First

The primary experience MUST be designed for iOS and Android mobile devices and delivered
as a Progressive Web App (PWA). The interface MUST adapt to larger screens without
compromising touch navigation. Installation, loading, and connection status MUST be
understandable in the mobile browsers covered by the delivery.

**Compliance criteria:**

- Critical flows MUST work without horizontal scrolling at widths of 320 CSS px and above.
- Each interface delivery MUST record validation in Safari on iOS and Chrome on Android,
  identifying versions, the physical device or test environment, and any limitations.
- The PWA MUST be served over HTTPS, have a valid manifest and icons, support installation
  through the mechanism available on each platform, and open in installed mode.
- Service worker behavior and version updates MUST be verified; an update MUST NOT leave
  the interface stuck on an incompatible application version.
- Connection loss MUST be indicated. The MVP MUST NOT promise offline writes without
  explicit reconciliation criteria and corresponding tests.

### IV. Security by Design

All access to playlists, ratings, and other private data MUST require authentication.
Every read, write, and event subscription MUST check authorization in a trusted layer,
on the server or in the database. Hiding interface controls MUST NOT be used as access
control. Activated participants may access data and perform actions only as explicitly
permitted by the approved sharing model. Membership MUST require a private invitation
and a single-use code to activate an account; the product MUST NOT impose a two-participant
business limit.

Privileged credentials MUST remain outside versioned code, client bundles, logs, and PWA
caches. Data received from clients MUST be validated at the trust boundary. The project
MUST limit data collection and exposure to what is necessary.

**Compliance criteria:**

- The specification MUST define a permission matrix for each resource and action,
  including authorship, shared reading, and rating changes.
- Tests MUST demonstrate that anonymous access, inactive accounts, expired sessions,
  unauthorized third parties, and attempts to change identifiers to access or modify another
  user's data are blocked.
- Activation tests MUST reject missing, invalid, and previously consumed invitation codes.
  Concurrent redemption of one code MUST activate at most one account. Account activation
  MUST NOT prevent a third participant from joining with a separate valid invitation.
- Isolation MUST cover queries, writes, real-time events, and caches; logout MUST prevent
  the next session from viewing private data from the previous session.
- Review MUST verify input validation, secure transport, session handling, and the absence
  of secrets in files, build artifacts, and logs.
- Required credentials MUST be configured through the environment's secrets mechanism;
  example files MUST contain only names and fictitious values.

### V. Real-Time Consistency

Confirmed playlist and rating changes MUST converge to the same persisted state in all
sessions authorized to view the affected data, without manual reloading. Persisted backend state MUST be
the source of truth. The interface MUST NOT present a write as completed before persistence
is confirmed. Concurrency, repeated requests, disconnection, and reconnection MUST have
defined behavior.

**Compliance criteria:**

- A test with two authenticated sessions MUST confirm that a persisted change appears
  in the other session within five seconds, with an active connection, an available service,
  and network conditions documented in the acceptance scenario, within free quotas.
- Concurrent operations on the same data MUST follow a resolution rule defined in the
  specification and tested; a change MUST NOT be silently lost.
- Retries and duplicate or out-of-order events MUST NOT create duplicate ratings or make
  the interface regress to an older version of the persisted state.
- After reconnecting, the session MUST retrieve the current state and converge before
  indicating synchronization is complete. While disconnected, it MUST indicate potentially
  outdated data and block writes or apply an already specified reconciliation policy.
- The synchronization solution MUST respect the MVP's permissions and free quotas.

### VI. Type Safety

Application code MUST use TypeScript with strict typing. Data contracts between the interface,
backend, and database MUST be explicit. External values MUST enter as untrusted data and
be validated at runtime before use; static types alone MUST NOT be treated as input validation.

**Compliance criteria:**

- All application TypeScript projects MUST enable `strict: true` without individually
  disabling its checks, and MUST pass type checking before delivery.
- First-party code MUST NOT use `any` without justification; unavoidable integrations with
  untyped code MUST be isolated at a documented boundary and validate incoming data.
- `@ts-ignore`, `@ts-nocheck`, and assertions used to hide errors MUST NOT replace contract
  corrections or data validation.
- Contract changes MUST update their consumers and keep type checking passing throughout
  the affected code.

### VII. Testable Development

Every critical feature MUST have observable acceptance criteria before implementation and
evidence of verification before delivery. At a minimum, authentication, authorization,
data isolation, playlist and rating persistence, synchronization, and failure recovery
are critical. Verification MUST be proportional to risk and focus on observable behavior
and data integrity.

**Compliance criteria:**

- Each critical requirement MUST have scenarios with context, action, and expected outcome,
  covering success, failure, and relevant boundaries.
- The plan and tasks MUST link these criteria to corresponding checks; delivery MUST record
  the results and how to reproduce them.
- Permission, persistence, and consistency rules MUST have automated tests at the appropriate
  level; usability checks may use a reproducible manual procedure.
- Fixed critical defects MUST receive a regression check that reproduces the issue.
  Failing mandatory tests or outstanding critical criteria block delivery.
- Tests MUST NOT depend on real personal data or modify the production environment.

### VIII. Database as Code

Every structural database change MUST be represented by versioned SQL migrations.
This includes tables, columns, indexes, constraints, functions, triggers, and access
policies used by the application. Manual dashboard changes MUST NOT be the source of truth.
The history MUST make it possible to reconstruct the database and understand data evolution.

**Compliance criteria:**

- Each migration MUST have a unique identifier and deterministic ordering, and be included
  in the same change set as the code that depends on it.
- Migrations already applied in a shared environment MUST NOT be rewritten; corrections
  MUST be made through subsequent migrations.
- Validation MUST apply the full history to an empty database and the new migrations to
  the previous supported version in an isolated environment, checking affected data and permissions.
- Destructive changes MUST document their impact, data preservation or transformation,
  and a verifiable recovery procedure through rollback, restoration, or a subsequent correction.
- Example data MUST be fictitious; credentials and private data MUST NOT be included
  in migrations. Differences between the database and migration history block delivery
  until corrected.

### IX. Accessibility

The interface MUST maintain readable text, sufficient contrast, and understandable
interaction through touch, keyboard, and assistive technologies. Essential information
MUST NOT depend exclusively on color, gestures, or visual position. Accessibility is
part of mobile flow acceptance and MUST be verified throughout development.

**Compliance criteria:**

- Regular text MUST have a minimum contrast ratio of 4.5:1; large text, 3:1. For this rule,
  large text means at least 24 CSS px, or 18.66 CSS px in bold. Essential controls and
  focus indicators MUST have a minimum contrast ratio of 3:1.
- Interactive targets MUST provide a minimum touch area of 44 by 44 CSS px.
- Enlarging text to 200% MUST NOT hide essential content, controls, or actions.
- Controls MUST have accessible names, forms MUST associate labels and errors with fields,
  and focus MUST be visible, logical, and free of navigation traps.
- Critical flows MUST be checked with a keyboard, VoiceOver on iOS, and TalkBack on Android;
  relevant error and synchronization messages MUST be perceivable without continuous
  visual attention.

### X. Maintainability

Components and modules MUST have clear responsibilities and understandable interfaces.
Operational documentation and relevant decisions MUST accompany delivered behavior.
Each dependency MUST address an identified need and justify its maintenance cost.
The project MUST NOT create generic structures without demonstrated current use.

**Compliance criteria:**

- Review MUST verify that affected components do not combine independent responsibilities
  without justification and that significant duplication has been evaluated.
- Changes to installation, configuration, execution, data, or public behavior MUST update
  the corresponding documentation in the same delivery.
- New dependencies MUST record their purpose, the alternative considered, maintenance
  status, license compatibility with the intended use, and effects on size, security,
  and infrastructure cost.
- Unused dependencies and code MUST be removed when identified; versions MUST be reproducible
  through a versioned manifest and lockfile.
- Dependency review MUST record relevant vulnerabilities and their treatment; a critical
  risk exploitable in the delivered flow blocks delivery until mitigation is verified.

## MVP Scope and Constraints

The MVP serves authenticated participants who join through private invitations and activate
an account using a single-use code. There is no two-participant business limit. Expected
membership capacity MUST be documented during planning against free infrastructure limits
and external provider eligibility; these constraints MUST NOT be misrepresented as a fixed
two-person product rule. Rating, ownership, and sharing rules MUST be defined in specifications;
this constitution does not assume a music provider, rating scale, or external integration.
Changes to the approved sharing model, social features, or audience require approved scope
before implementation and a new assessment of cost, security, and simplicity.

The solution MUST combine a PWA web application, strict TypeScript, and a database with
SQL migrations. The framework, hosting, database, authentication, and synchronization
mechanism MUST be selected during planning with evidence of compliance with all ten
principles. This constitution does not mandate vendors or assume free offers will remain unchanged.

Caching and offline operation MUST prioritize data protection and consistency. The MVP
may limit offline use to the interface and unavailability messages; no promise of private
offline reading or offline writing may be added without specified and verified permissions,
invalidation, and reconciliation.

## Development Workflow and Delivery Criteria

1. **Specification:** every feature MUST state its scope, data, actors, permissions,
   failure behavior, and verifiable acceptance criteria. Applicable principles MUST
   be identified by number or name.
2. **Planning:** each plan MUST include a review of all ten principles, with a decision,
   justification, and planned evidence for each. A non-applicable item requires justification.
   Free limits, risks, contracts, and migrations MUST be evaluated before implementation.
3. **Tasks:** requirements and compliance criteria MUST be linked to concrete tasks,
   including relevant tests, documentation, and security, mobile, and synchronization checks.
4. **Implementation and review:** every change MUST follow the plan and undergo compliance
   review, even when performed by a single person or an automated agent. Differences
   MUST be resolved or referred to the amendment procedure.
5. **Delivery:** review MUST record results from type checking, critical tests, migrations,
   and applicable mobile and accessibility checks, as well as confirmation of zero cost
   and credential protection. Evidence may be attached to the review artifact without
   requiring an additional documentation system.

A delivery is complete only when its applicable acceptance and compliance criteria
are satisfied. Lack of time MUST NOT be used to waive security, data integrity, or any
other mandatory requirement of this constitution.

## Governance

This constitution is the project's source of governance and takes precedence over conflicting
specifications, plans, tasks, technical decisions, and local practices. People and automated
agents MUST consult it before specifying, planning, implementing, and reviewing changes.
All future Spec Kit workflows MUST evaluate compliance with the current version.

Amendments MUST record their motivation, previous and proposed wording, affected principles,
impact on requirements and implementation, resulting version, and adaptation plan.
Explicit approval from the project owner MUST occur before an amendment takes effect.
The owner requesting the initial adoption ratified version 1.0.0 on 2026-11-18.
On 2026-11-19, the owner explicitly replaced the two-participant assumption with private,
single-use invitation activation; version 2.0.0 records that approved correction.
On 2026-11-19, the owner requested the narrow existing personal Spotify Premium exception;
version 3.0.0 records that approved amendment without authorizing new project spending.
Future changes MUST preserve the ratification date and update the amendment date.

Versioning MUST follow `MAJOR.MINOR.PATCH`:

- **MAJOR:** removal, incompatible relaxation, or redefinition of a principle or governance
  rule that changes existing obligations.
- **MINOR:** addition of a principle, section, or material guidance compatible with
  existing obligations.
- **PATCH:** clarification, editorial correction, or adjustment without changing obligations.

Every delivery review MUST identify the constitutional version used and record compliance
or noncompliance with all ten principles. Exceptions that change obligations MUST follow
the amendment procedure; a plan, review comment, or isolated technical decision MUST NOT
authorize noncompliance. When an amendment affects ongoing work, the corresponding artifacts
and checks MUST be updated before the next delivery.

**Version**: 3.0.0 | **Ratified**: 2026-11-18 | **Last Amended**: 2026-11-19
