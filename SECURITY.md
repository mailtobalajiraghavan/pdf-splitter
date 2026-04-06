# Security Policy

PDF Studio is a privacy-first, fully client-side tool. This document explains how the project handles security and how to report issues.

## Reporting a vulnerability

**Please do not open a public GitHub issue for security problems.**

Instead, report privately via either:

- GitHub's [private security advisory](https://github.com/mailtobalajiraghavan/pdf-studio/security/advisories/new) (preferred)
- Email the maintainer directly through the contact link on [madebybalaji.com](https://madebybalaji.com)

Please include:

- A clear description of the issue
- Steps to reproduce (or a proof-of-concept HTML/PDF)
- Affected version / commit hash
- Your assessment of impact

You can expect an initial acknowledgement within a few days. Critical issues will be patched as quickly as possible; lower-severity findings will be triaged on a best-effort basis (this is a side project, not a commercial product).

## Threat model

PDF Studio runs **entirely in the user's browser**. There is no backend, no upload, no account, no persistent storage, no shared state between users. This shapes what's in scope and what isn't:

### In scope

- XSS / HTML injection in any UI surface
- Tampering with the third-party libraries the app depends on
- Code paths that could leak the contents of one user's PDF to another origin
- Bugs that could let a malicious PDF execute code outside the pdf.js / pdf-lib sandbox
- Incorrect cryptographic claims (e.g. password protection that doesn't actually protect)

### Out of scope

- Issues that require physical access to the user's device or compromised browser
- Social engineering of the user into uploading sensitive files (the tool is local — there is no upload)
- Bugs in the user's own browser, OS, or browser extensions
- Denial of service caused by the user opening their own enormous PDF
- Findings that require disabling the Content Security Policy or SRI hashes
- Findings against forks that have removed the security headers

## Hardening already in place

- **Subresource Integrity (SRI)** with SHA-384 hashes on all CDN scripts (`pdf.js`, `pdf-lib`, `jszip`). Tampered libraries will fail to load.
- **Strict security headers** via `netlify.toml`:
  - `Content-Security-Policy` restricting script/style/connect sources
  - `X-Frame-Options: DENY`
  - `X-Content-Type-Options: nosniff`
  - `Referrer-Policy: strict-origin-when-cross-origin`
  - `Permissions-Policy` denying camera, microphone, geolocation, and FLoC
- **Filename sanitization** strips path separators, control characters, and Windows-reserved names from every download.
- **Password fields are cleared** from the DOM immediately after the encrypt/unlock operation completes.
- **No `localStorage`, `sessionStorage`, `indexedDB`, or cookies.** Nothing about your PDFs persists across reloads.
- **Magic-byte validation** (`%PDF-`) on every uploaded file in addition to the extension check.
- **MIT-licensed source** — anyone can audit the entire app in a single 2,200-line `index.html`.

## Known limitations

These are tracked, documented, and not currently considered emergencies:

### `pdf.js` version

The app uses **pdf.js 3.11.174**, which is affected by [CVE-2024-4367](https://nvd.nist.gov/vuln/detail/CVE-2024-4367) (arbitrary JS execution via a crafted font in a PDF, fixed in 4.2.67).

**Why it's not yet upgraded:** pdf.js 4.x dropped the UMD build and ships only as ES modules (`.mjs`), which would require restructuring how the app loads and calls the library.

**Why the impact is limited here:** pdf.js is used only to render small page thumbnails for the visual split UI. There is no cross-user state, no server, no persistent storage. A malicious PDF crafted to exploit this CVE can only affect the current tab of the user who deliberately opened it — and that user already has the file.

**Mitigation:** the SRI hash locks the version against tampering. An upgrade to pdf.js 4.x is planned.

### CSP `unsafe-inline` and `unsafe-eval`

The Content Security Policy currently allows `'unsafe-inline'` (the app's logic lives in an inline `<script>` block) and `'unsafe-eval'` (required by some pdf.js code paths).

**Why the impact is limited here:** the app processes **only PDF files** as user input. No text input is ever rendered into HTML. The `esc()` helper escapes filenames before they touch any `innerHTML`. There is no realistic XSS vector.

**Mitigation planned:** extract the inline JS into an external `app.js` so `'unsafe-inline'` can be dropped.

## Security-relevant changes

Material security changes are noted in commit messages and, for any disclosed CVE, in this document. For a full audit trail, see the [git log](https://github.com/mailtobalajiraghavan/pdf-studio/commits/master).

## Disclosure policy

- **Coordinated:** I'll work with you on a fix, then credit you (if you'd like) when the patch ships.
- **No bug bounty:** this is an unpaid side project. A thank-you and credit are all I can offer.
- **No legal action:** good-faith security research is welcome and appreciated.

Thanks for helping keep PDF Studio safe.
