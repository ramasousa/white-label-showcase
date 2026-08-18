# CLAUDE.md

Guidance for AI assistants working in this repository.

## What this repository is

A **showcase of self-contained HTML prototypes and technical documents** for the
Bradesco *BaaS White Label* platform: one Shell App, many partners, credit
products (Capital de Giro, Crédito Emergencial, Antecipação de Recebíveis), and a
visual identity that is swapped at runtime via design tokens.

There is **no build system, no package manager, no framework, no test suite, and
no CI**. The repository is a flat directory of `.html` files at the root. Each
file is a complete, standalone deliverable: HTML + inline `<style>` + inline
`<script>`, served straight from disk or GitHub Pages.

Consequences for any change:

- **Never** introduce `package.json`, bundlers, npm dependencies, TypeScript, or
  a `src/` layout unless the user explicitly asks for it.
- **Never** extract shared CSS/JS into external files. Duplication across files
  is intentional — each page must keep working when opened alone via `file://`.
- The only permitted external request is the Google Fonts `<link>` in `<head>`.
  Everything else (icons, illustrations, noise textures) is inline SVG or a
  `data:` URI.

## Repository map

`index.html` is the hub: every other file is reachable from it, grouped into five
sections (`#jornadas`, `#multiproduto`, `#runtime`, `#arquitetura`,
`#estrategia`). Links are plain relative hrefs (`href="cg-portal-completo-v4.html"`).

### Journey prototypes — the three consumption models
| File | What it is |
|---|---|
| `cg-portal-completo-v4.html` | **B2B**, the most complete prototype (~2400 lines). Operator portal: MFA/SSO login, RBAC (Gerente `Ana Lima` vs Operadores `Carlos Melo` / `Fernanda Rocha`), 3 credit products, 5-step proposal wizard, mock proposal list. Step 5 ends in a **handoff**: the operator never contracts — a link goes to the client, whose device (mocked as a phone beside the flow) reviews the imputed data, confirms the PIX key and signs. Carries the same dark **Runtime drawer** as the Cacau Show prototype, showing RBAC scopes with `credit:apply` struck through for every portal role. |
| `cg-cliente-final-b2c.html` | **B2C** self-service mobile journey, 4 screens, consumer language, no banking jargon. |
| `cg-embedded-b2b2b-cacaushow.html` | **B2B2B** embedded MFE inside the Cacau Show franchisee portal — no chrome, no extra login. Right-hand panel has two tabs: **Runtime** (what happens under the hood — token exchange, security controls, MFE load, driven by the user's own actions) and **Design System** (the live token inspector). The panel deliberately keeps the dark `--shell-*` palette so it reads as *the platform* against the partner's branding. |

### Shell / runtime demos (animated, step-driven)
| File | What it is |
|---|---|
| `shell-multitenant-demo.html` | Two tenants (Vivo & Claro) booting side by side, 10 logged steps each. |
| `shell-flow-visual.html` | Shell initialization flow, visual. |
| `shell-runtime-4panel.html` | Federated vs standalone runtime, four panels. |
| `shell-federated-vs-standalone.html` | Comparison of the two loading strategies. |
| `design-token-import-demo.html` | Streams partner tokens (Cacau Show, O Boticário) into a live MFE via `style.setProperty`. |

### Architecture & platform docs
| File | What it is |
|---|---|
| `baas-whitelabel-frontend-arch.html` | The reference document (~1750 lines): stack, MFE interface contract, 3 customization layers, token exchange / UCT JWT claims, storage & revocation, `postMessage`, versioning, CI/CD per MFE, Module Federation config, AWS+Azure hybrid. |
| `api-flow-tokens-registry-mfe.html` | End-to-end API flow: partner tokens, customer registry, MFE APIs. |
| `bff-token-flow.html` | Detailed BFF token flow. |
| `mfe-registry-dinamico-arquitetura.html` | Adding/removing products with zero deploy. |
| `baas-admin-mfe-registry-v2.html` | Admin UI for the MFE registry / portfolio management. |
| `baas-seguranca-arquitetura.html` | Security architecture: STRIDE threat model across 6 trust boundaries (21 threats), MFE supply-chain integrity, what "MFE isolation" really means, CSP/DPoP/PAR, regulatory mapping, incident runbooks, plus two structural decision sections (who builds/hosts the MFE — the Aarin scenarios; LGPD controller roles) and a go-live checklist. Uses the **Velo design system** palette (cream/ink), not the dark BaaS one. |
| `baas-modelos-identidade.html` | **Identity models**: federated (partner is the IdP) vs standalone (Bradesco is the IdP — the "white label full" for partners with no IT) vs hybrid. Carries the 9-step user-lifecycle responsibility matrix (who onboards, who resets passwords, who answers support), the login-domain/anti-phishing decision, what changes in the token flow when there is no Token Exchange, 6 standalone-only threats, and 7 open decisions. Velo palette, companion to the security doc. |
| `baas-originacao-cliente-final.html` | **Client origination**: how the eligibility base arrives from a partner with no IT (6-channel ladder — link/QR, invite, spreadsheet upload, SFTP, ERP connector, API — each with a level of assurance), the 7-step KYC funnel for a non-accountholder, disbursement to same-titularity only, the 4 distribution channels (web, mobile/PWA, embedded, assisted), and the *correspondente no País* framing that assisted operation triggers. **Assisted contracting is a decided fact**, so it carries the 11-step end-to-end flow (operator imputes → handoff → client reviews → client signs on their own device), the rules for file upload and for the digital-signature evidence package, 10 threats and 8 open decisions. Velo palette. |
| `baas-execution-roadmap.html` | Execution roadmap: squads, phases (each with its own color tokens). |

### Strategy
| File | What it is |
|---|---|
| `decisao-estrategica-bradesco-aarin.html` | Bradesco vs Aarin vs hybrid decision deck. Screen version, with sidebar navigation (`.sb-*` classes). |
| `decisao-estrategica-print.html` | Same content, print/PDF variant: sidebar and `.section` wrapper removed, `@media print` disables animations. **Keep the two in sync when the content changes.** |

## Conventions

### Document skeleton
Every file follows the same shape — match it exactly when creating a new page:

```html
<!DOCTYPE html>
<html lang="pt-BR">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Área · Assunto — Qualificador</title>
<link href="https://fonts.googleapis.com/css2?family=...&display=swap" rel="stylesheet">
<style> /* reset, :root tokens, then component CSS */ </style>
</head>
<body> ... <script> ... </script> </body>
</html>
```

- `lang="pt-BR"` always. **All user-facing copy is Brazilian Portuguese** —
  titles, labels, logs, button text, tooltips. Do not translate to English.
- Titles use `·` and `—` as separators, e.g.
  `CG · Portal Operador · Multi-Produto · RBAC`.
- CSS starts with `*{margin:0;padding:0;box-sizing:border-box}`.

### CSS
- **Design tokens live in a single `:root` block** at the top of the `<style>`.
  The palette is per-file, not global — dark surfaces (`--bg`, `--s1`…`--s4`),
  translucent borders (`--border`, `--b2`, `--b3`), text ramp
  (`--text`, `--muted`, `--dim`), and accent triads: base color + `*g` glow fill
  + `*b` border (e.g. `--red` / `--rg` / `--rb`). Font tokens are
  `--mono`, `--sans` (or `--body`), `--disp`.
- Two files intentionally break the dark default: `cg-portal-completo-v4.html`
  (light corporate UI, `--brand:#CC0000`) and the `decisao-estrategica-*` pair
  (warm paper `#f7f6f2`, serif body). Respect the local palette of the file you
  are editing; never impose one file's tokens on another.
- Declarations are written **compactly, one rule per line, no space after `:`**.
  Follow the existing density instead of reformatting.
- Section dividers in CSS and HTML use box-drawing comments:
  `/* ═══ NAV ═══ */`, `<!-- ═══ HERO ═══ -->`, `// ── CONFIG ──`.
- Class names are short, prefixed abbreviations scoped to the component:
  `.nav-link`, `.sec-eyebrow`, `.jc-title`, `.ac-badge`, `.ti-row`, `.sb-link`.
- Responsive: at most one or two `@media (max-width: …)` blocks at the end.
  `@media print` only exists in `decisao-estrategica-print.html`.

### JavaScript
- Plain ES6+, no modules, no imports, one `<script>` at the end of `<body>`.
- Structure it in the established order with banner comments:
  **CONFIG / DATA → STATE → HELPERS → actions → `DOMContentLoaded` init**.
- Mock data lives in `const` UPPER_SNAKE objects/arrays at the top
  (`USERS`, `PRODUCTS`, `ALL_PROPOSALS`, `CACAU_TOKENS`, `BOTI_TOKENS`).
- Mutable state is a handful of module-level `let`s (`currentUser`,
  `currentProduct`, `running`, `timers`).
- Handlers are **global `function` declarations wired via inline `onclick=`**
  in the markup (`showPage()`, `startFlow()`, `resetAll()`, `wizNext()`,
  `setPartner()`). This is deliberate — do not refactor to `addEventListener`
  delegation or ES modules.
- Animated demos drive themselves with `setTimeout` chains pushed into a
  `timers` array, plus a `reset*()` that clears them — always clear timers on
  reset so replays don't overlap.
- Runtime theming is done with
  `document.documentElement.style.setProperty('--token', value)` (or the same on
  the MFE container element). That mechanism *is* the point of the showcase;
  keep it visible and inspectable.
- Naming follows the domain in Portuguese where the data is Portuguese
  (`parcela`, `prazo`, `carencia`) and English for plumbing (`step`, `timers`).

### Fictional data
All names, CNPJs, values, partners (Vivo, Claro, Cacau Show, O Boticário) and
URLs (`https://*.baas.bradesco.com.br`) are **mock content for a prototype**.
Keep them plausible and internally consistent; never wire a page to a real
endpoint, credential, or token.

## Working on this repository

### Running / verifying
There is nothing to build. Open the file:

```bash
python3 -m http.server 8000   # then browse http://localhost:8000/index.html
```

`file://` also works for every page — verify that it still does after a change.
Manual verification is the only test: click through the flow you touched, and
check both the page itself and its entry point in `index.html`.

### Checklist when adding a new page
1. Create `<nome-descritivo-em-kebab-case>.html` at the repository root.
2. Copy the document skeleton and the `:root` token block of the closest sibling.
3. Add a card linking to it in the matching `index.html` section — one of
   `.journey-card`, `.artifact-card`, or `.doc-row`, following the surrounding
   markup exactly (icon, badge, title, description, footer arrow).
4. Update the hero stats in `index.html` if the counts change.

### Git workflow
- Work on the branch you were assigned; commit with clear messages.
- Push with `git push -u origin <branch>`.
- Do not open a pull request unless the user explicitly asks for one.
- Because files are large and single-purpose, prefer **targeted edits** over
  rewriting a whole file — a full rewrite loses hand-tuned spacing and copy.

### Always report delivery status (required)
The user asked to be told this every time, unprompted. At the end of any change,
state plainly:

1. **Where the work is** — which branch, and **whether it reached `main`**. Say
   "not on `main` yet" explicitly; never let a feature branch be mistaken for
   something that is live. If a PR exists, give its number and its real state
   (open / merged / closed **without** merge).
2. **How to test it right now** — these pages are standalone HTML, so a change
   is testable the moment it is pushed: no merge, no build, no server. Give the
   concrete route: the file sent directly in chat, `git fetch origin <branch>`
   then opening it over `file://`, or the file on the branch in GitHub.

**GitHub Pages is live for this repository and serves `main`.** So a change on a
feature branch is testable immediately by the routes above, but the published
site only reflects it **after the merge** — always make that distinction
explicit rather than letting "it's done" imply "it's online".

Re-send the file in chat whenever it changed meaningfully since the last send,
so the user is never testing a stale copy.
