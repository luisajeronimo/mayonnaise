# Dependency Compatibility and Execution Evidence

**Evidence scope:** Exact core-stack and added SMTP dependency probes only. The approved
VIP-playlist/catalog-album scope is recorded in [spec.md](spec.md). These checks do not
constitute full application or live-provider acceptance.

**Validation date**: 2026-11-20
**Scope**: Requested stack; temporary project outside the repository. No live application
or cloud resource was deployed. [Plan](plan.md) and [research](research.md).

## Selected and tested versions

| Package/runtime | Version observed |
| --- | --- |
| Node.js / npm | 24.20.0 / 11.19.0 |
| next | 16.4.6 |
| react / react-dom | 19.3.1 / 19.3.1 |
| typescript | 5.9.3 |
| tailwindcss / @tailwindcss/postcss | 4.3.5 / 4.3.5 |
| postcss | 8.5.33 |
| lucide-react | 1.76.0 |
| @supabase/supabase-js / @supabase/ssr | 2.119.2 / 0.12.7 |
| zod | 4.6.7 |
| vitest / test-only vite | 5.1.1 / 8.3.8 |
| @playwright/test | 1.65.1 |
| @types/node | 24.19.2 |
| @types/react / @types/react-dom | 19.3.0 / 19.3.0 |

The installed SSR peer range ^2.114.0 accepts Supabase JS 2.119.2. Next's React and
Playwright peers also accepted the selected versions. npm ls --all completed successfully.
Production remains a Next application; Vite is only a Vitest dependency.

## Observed failures and chosen correction

1. TypeScript 6.0.3 with current Supabase failed TS2430 in auth-js WebAuthn declarations:
   PublicKeyCredentialFuture.toJSON conflicted with the changed DOM JSON extension shape.
   The largeBlob field differed between ArrayBuffer and string representations.
2. TypeScript 5.9.3 removed that error, but Next 16.4.6's URLPattern declarations referenced
   two platform names absent from that compiler's DOM library.
3. Add only these platform definitions in types/next-urlpattern.d.ts, included by tsconfig:

~~~ts
type URLPatternInput = string | URLPatternInit;
interface URLPatternOptions { ignoreCase?: boolean }
~~~

These describe standard URLPattern input/options; they do not replace runtime code or
weaken types with any/assertions. Keep strict:true, skipLibCheck:false and normal Next
build-error enforcement. No dependency file was patched and no diagnostic was suppressed.

Next supports TypeScript >=5.1.3 for async Server Components and documents newer CLI
integration; it is incorrect to claim TypeScript 6 is a hard Next requirement. TypeScript
5.9.3 is the tested compatibility pin, not the latest overall release. Remove the bridge
and upgrade only after rerunning the same full check against fixed upstream declarations.

**References:** [Next TypeScript](https://nextjs.org/docs/app/api-reference/config/typescript),
[TS 5.9 DOM source](https://raw.githubusercontent.com/microsoft/TypeScript/v5.9.3/src/lib/dom.generated.d.ts),
[TS 6 DOM source](https://raw.githubusercontent.com/microsoft/TypeScript/v6.0.3/src/lib/dom.generated.d.ts),
[Node URLPattern](https://nodejs.org/api/url.html#class-urlpattern),
[WHATWG URL Pattern Standard](https://urlpattern.spec.whatwg.org/#the-urlpattern-class).

## Executed checks

| Check | Observed result |
| --- | --- |
| npm install and npm ls --all | Passed; concrete lockfile and peer tree resolved. |
| tsc --noEmit | Passed with strict:true and skipLibCheck:false. |
| next build | Passed production Turbopack build and Next's type check. |
| Vitest | Two SDK API/runtime-boundary compatibility tests passed. |
| Playwright | One Chromium test passed using two independently created browser contexts. |
| Browser checks | Separate cookie-derived dynamic Server Component identities, isolated Client Component state, Tailwind styling, Lucide SVG, Supabase server/browser construction and manifest response. |

The temporary lockfile SHA-256 was
c3829b6a25072a3a5c08fab0b21316387c72b92dc1418068d1c37b0ddea1e156.
A Vitest configuration warning was resolved by using vitest.config.mts and the unit
check was rerun successfully. Dependencies were installed without package lifecycle scripts;
the tested build/browser commands executed explicitly. No global tooling upgrade occurred. Installed package manifests recorded MIT for
Next/React/Tailwind/PostCSS/Supabase/Zod/Vitest/Vite, Apache-2.0 for TypeScript and
Playwright, and ISC for Lucide. Preserve notices and review transitive licenses and
vulnerabilities with the implementation lockfile; no full security audit is claimed here.

## What these checks do not establish

The fixture uses fictitious identities and an invalid Supabase URL. It does not establish
real Supabase email delivery, Auth/session revocation, RLS, SQL role correctness,
Postgres Changes delivery, or five-second participant synchronization. Those require
the genuine local/authorized hosted acceptance scenarios in [quickstart.md](quickstart.md).

No Vercel deployment, Gmail credential setup, GitHub billing/CI configuration, real
Spotify import, actual mobile installation or full application test suite was performed.
The planning command creates design documents only. Do not promote dependency-probe
results into a claim that the Mayonnaise MVP is implemented or tested.

## Added SMTP compatibility probe — 2026-11-20

Temporary directory: `/tmp/mayonnaise-mail-stack-gsojuhgr`. It copies the earlier
fictional full-stack fixture and adds a server-only Node route constructing a typed
nonpooled SMTP transport without sending. No project package/app was created.

| Item | Verified result |
| --- | --- |
| Runtime | Node 24.20.0, npm 11.19.0. |
| Runtime library | Nodemailer 10.6.1, MIT-0, Node >=20; no runtime dependencies. |
| Types | @types/nodemailer 8.0.2, MIT; @types/node deduplicated to 24.19.2. |
| Install/tree | npm install --ignore-scripts and npm ls --all pass. |
| Strict check | npm run typecheck passes, strict:true and skipLibCheck:false. |
| Tests | npm test: 7 pass, including 5 actual 127.0.0.1 SMTP tests and 2 original core units. |
| Production | NEXT_TELEMETRY_DISABLED=1 npm run build -- --webpack passes compilation, types, prerender and server route tracing. |
| Security snapshot | npm audit reports zero findings for this temporary lockfile on this date. |

SMTP scenarios: sole-recipient acceptance; 550 recipient rejection before DATA;
acknowledgement timeout after the peer receives the complete message; verify without
sending; explicit AbortSignal cancellation after full message receipt. The last two
ambiguous cases must not be interpreted as proof of non-delivery.

Runtime cancellation supports a signal field absent from the current types. The probe
checks a narrow `SMTPTransport.MailOptions & { signal: AbortSignal }` intersection.
Keep it isolated, documented and rechecked on upgrade; do not suppress type diagnostics.

Earlier restricted child attempts failed loopback binding/DNS and did not establish
behavior. Root subsequently ran the successful checks above. Turbopack production
compilation still hit worker-binding EPERM; the supported bundled Webpack build passed.
Use `next build --webpack` in the reproducible production script. This changes a Next
CLI option, not the application's stack or dependency list.

SHA-256:

| Artifact | Digest |
| --- | --- |
| package-lock.json | `3b44a55c56983d7a5f3bd79e5144dc424e5289681ae7bbb26cf277b0f28268aa` |
| tests/mail.test.ts | `653c68f5b0163bba869d00444361eff44f9aebf386c483cb8f6334d17c29037b` |
| app/api/mail-compatibility/route.ts | `38b0aecf69fffa291e13c3a16817e772684131ce0d96d0e72f1c0c128cc77a4f` |

No Gmail authentication, production TLS handshake, actual mailbox delivery, deployed
Vercel connection or revised MVP E2E was tested. The loopback fixture deliberately
disables TLS only locally. License/version sources: installed package metadata,
[npm registry](https://registry.npmjs.org/nodemailer/10.6.1),
[type package](https://registry.npmjs.org/@types/nodemailer/8.0.2),
[Nodemailer SMTP](https://nodemailer.com/smtp),
[message cancellation](https://nodemailer.com/message),
[Next CLI](https://nextjs.org/docs/app/api-reference/cli/next).

## Local database tooling smoke check — 2026-11-20

Supabase CLI 3.2.0 (MIT) starts through npm under Node 24.20.0/npm 11.19.0.
Version, start --help, db reset --help and test db --help all pass. Docker CLI reports
29.9.3. No containers, migrations or application SQL tests were run.

CLI 3 uses local-by-default db reset/test and --remote for remote targets; --local is
not an accepted reset flag. Keep local-only guards in test scripts. The official release
supports PostgreSQL 17; use it in local config. Pin CLI 3.2.0 as development-only tooling
and include it in the final project audit/lockfile, beyond the SMTP fixture audit scope.

Sources: [CLI setup](https://supabase.com/docs/guides/local-development/cli/getting-started),
[3.2.0 release](https://github.com/supabase/cli/releases/tag/v3.2.0).
