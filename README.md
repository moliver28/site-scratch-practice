# Scratch Practice Site — karahokes.com rebuilt on sitefactory

A therapy-practice site built with [sitefactory](https://github.com/moliver28/sitefactory)
(practice schema-pack, journalism template v0.1.0, @moliver28/theme 0.9.0,
`clinical-calm` preset). It rebuilds the content model of
[moliver28/karahokes-website](https://github.com/moliver28/karahokes-website)
(www.karahokes.com) as pack DATA — facts transcribed from the bench repo
(read-only reference; no bench markup, TSX, CSS, or binaries copied).

- Live: https://moliver28.github.io/site-scratch-practice/ (Pages is enabled
  after the initial PR merges; deploy consumes the CI build artifact)
- PR previews: `https://moliver28.github.io/site-scratch-practice/pr-preview/pr-<n>/`
- Efficiency accounting: [docs/efficiency.md](docs/efficiency.md)

## Structure

| Path | What it is |
| --- | --- |
| `src/content/entities/*.yaml` | 64 typed practice entries (profile, services, communities, FAQs, insurance plans, concerns, process steps, books, resources) |
| `src/content/pages/*.md` | Markdown pages (crisis resources) |
| `site.config.yaml` | Site identity + the composed `home:` spec (10 anchored sections) + `pages:` (referrals) |
| `src/components/DiscoveryHead.astro` | OG/Twitter/JSON-LD head tags + this site's og:image extension |
| `static/social-card.png` | 1200x630 OG card (clinical-calm palette) |
| `static/dr-kara-hokes.vcf` | Downloadable vCard, CI-synced to the profile entity |
| `static/robots.txt` | Allow-all + sitemap pointer |
| `static/admin/` | Sveltia CMS (staff-only, `/admin/`, practice vocabulary) |
| `vendor/theme/` | Vendored `@moliver28/theme` (pinned via `site.config.yaml`) |
| `.github/workflows/ci.yml` | gates (build artifact) → quality (axe + Lighthouse + lychee) → deploy/preview/cleanup |
| `.github/workflows/monitor.yml` | Weekly link check of the deployed site |
| `.github/workflows/vcf-sync.yml` | vCard ↔ profile phone/email sync pin |
| `docs/efficiency.md` | Rebuild LOC per class + ratio math (target: beat 3.4x) |
| `AGENTS.md` | Template-emitted rules (recopy-managed; see practice notes below) |

## Parity — all 12 benchmark sections mapped

The benchmark home is one page with 12 anchored sections. Every row is
either SHIPPED on this site or OUT **with the surface that replaces it** —
nothing is lost by omission. Bench files cited as facts-only sources.

| # | Bench section | Surface on this site | Status |
| --- | --- | --- | --- |
| 1 | `#home` hero (`hero.tsx`) | `hero` block (kicker/headline/lede props, composed home) | SHIPPED — the two in-page CTA buttons degrade to the section nav + contact card: the theme's hero `cta:` list accepts http(s) URLs only (B14 gate), anchors are carried by the nav |
| 2 | `#about` (`about.tsx`) | `fields` block ← `profile` (summary prose, credentials, license # + verify link, PSYPACT) | SHIPPED — pull-quote degrades to prose (the `quote` block exists but is not bound); portrait omitted (practice-owned photo, not copied — a live practice drops its file at the same path) |
| 3 | `#communities` (`communities.tsx`) | `cards` block ← `community` × 10 | SHIPPED |
| 4 | `#specialties` (`specialties.tsx`) | `cards` block ← `service` × 9, badge chips ride the `badge` field type | SHIPPED |
| 5 | `#concerns` (`concerns.tsx`) | `fields` block ← `concerns` (16-item list) | SHIPPED |
| 6 | `#grounding` + 5-4-3-2-1 senses (`grounding.tsx`, `senses-grounding.tsx`) | — | OUT by design — interactive therapeutic tools need JS-island infrastructure the theme deliberately does not ship; crisis paths are carried by the crisis-resources page, the crisis notice fields, and the print sheet |
| 7 | `#approach` (`approach.tsx`) | `timeline` block ← `process_step` × 4 | SHIPPED |
| 8 | `#reading` (`reading.tsx`) | `cards` blocks ← `book` × 4 + `resource` × 5 (bookshop.org/external links ride `url` fields) | SHIPPED — book covers omitted (practice/publisher-owned binaries, not copied) |
| 9 | `#insurance` (`insurance.tsx`) | `cards` block ← `insurance_plan` × 16; fee/self-pay/Good-Faith-Estimate facts live in the profile + FAQ answers | SHIPPED |
| 10 | `#faq` (`faq.tsx`) | `faq` block ← `faq` × 14 — native `<details>`, zero JS; the SAME entries emit the FAQPage JSON-LD node (single-source invariant is structural) | SHIPPED |
| 11 | `#contact` (`contact.tsx`) | `fields` block ← `profile` (phone → `tel:`, email → `mailto:`, hours, fee, location, website, Psychology Today) + crisis notice + print stylesheet + vCard file (CI-synced) | SHIPPED — consultation form + Worker backend OUT by design (HIPAA-adjacent intake stays out; the tel/mailto contact card is the shipped truth) |
| 12 | `#providers` (referrers, `providers.tsx`) | composed page `/pages/referrals/` (hero + fields, 4 referrer subsections) | SHIPPED |

Site-level parity (the bench's non-section surface):

| Bench feature | Surface on this site | Status |
| --- | --- | --- |
| JSON-LD `@graph` (Psychologist/Person/WebSite/FAQPage) | One `ld+json` script on the home, generated from the same entities the sections render | SHIPPED |
| Sitemap, robots.txt, canonical, OG/Twitter cards | `@astrojs/sitemap`, `DiscoveryHead`, `static/robots.txt`, `static/social-card.png` + og:image tags | SHIPPED |
| Downloadable vCard + CI sync (`site.ts ↔ vcf ↔ worker`) | `static/dr-kara-hokes.vcf` + `vcf-sync.yml` (one YAML source + one grep — no worker to sync) | SHIPPED (narrowed to what exists) |
| Print crisis sheet + print button | `PrintStyles` hook on the composed home (crisis + contact one-pager) | SHIPPED |
| Sticky scrollspy header + mobile Sheet nav | Static anchor nav + skip link (the pack's 9-anchor sections index) | OUT — zero-client-JS policy; anchors keep deep links working |
| Back-to-top, next-section button, framer-motion reveals | Native anchors; no scroll JS | OUT — zero-client-JS policy |
| Structured 3-column footer | `BaseLayout` footer slot (site identity + author line) | OUT — structured footer is catalog row D32, not scheduled this wave |
| Consultation form + Cloudflare Worker backend | Contact card (`tel:` / `mailto:`) | OUT by design — see #11 |
| Breathing widget + senses stepper | Crisis-resources page + print sheet | OUT by design — see #6 |
| Cloudflare analytics beacon | `analytics: none` | OUT — privacy-first policy |
| Geist / Source Serif 4 webfonts | `clinical-calm` token system stacks | OUT — no-webfonts policy |
| Dark mode (`prefers-color-scheme` sheet) | Light-only `clinical-calm` identity | OUT — catalog E36, flagged not scheduled |

## Practice notes (binding for agents editing this repo)

- **Facts live in exactly one place.** Entity YAML under `src/content/` is
  the single source; the home's sections only BIND it via `source:`. Never
  restate a fact in `site.config.yaml`, the vCard, or a page — the
  `vcf-sync.yml` pin fails the build on phone/email drift, and the JSON-LD
  @graph reads the same entries the sections render.
- **`src/content.extensions.ts`** is the client-owned schema seam
  (survives recopy). Everything else under `src/` is template-emitted;
  this repo's deliberate emitted-file edits (re-assert after a
  `copier recopy`) are:
  1. the og:image block in `DiscoveryHead.astro` (the path the template's
     own comment documents), and
  2. the a11y token override in the same file — clinical-calm's
     `--sf-color-accent` (#0d9488) is 3.6:1 as small-text ink on the
     preset background, below the 4.5:1 AA floor the emitted axe gates
     enforce; the override swaps it to the preset's own link teal
     #0f766e (5.2:1) site-wide. Upstreamable: fix the preset token —
     the factory's practice-smoke never caught this because it renders
     with the default preset (`--data pack=practice` only).
  3. the `head-extra` mount in `src/routes/composed-pages/[...id].astro` —
     the emitted route ships without DiscoveryHead, so composed pages had
     no OG/canonical tags and none of the site token overrides; this
     brings them in line with every other wired route.
- `AGENTS.md` is template-emitted and still carries the journalism pack's
  framing; the practice rules here govern. (Upstreamable: pack-aware
  AGENTS.md emission.)

## Commands

```sh
npm install
npm run dev        # local dev server
npm run build      # production build (Zod validation fails on bad data)
npm run typecheck  # astro check
```

Deploys: push to `main` → GitHub Pages at
`https://moliver28.github.io/site-scratch-practice/`; PRs get previews at
`/pr-preview/pr-<n>/` (auto-cleaned on merge). Deploy ships exactly what
the gates job built (artifact handoff — no second build, no drift).
