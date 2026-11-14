# Efficiency — karahokes.com rebuild vs the benchmark repo

Method: the P14 gap-analysis method (`karahokes-gap-analysis.md` in the
sitefactory repo), applied to the shipped rebuild. Base is the pinned
comparable scope: **(6,323 − 1,850) = 4,473 LOC**. Rebuild LOC is counted
with cloc 2.12 (`npx cloc`, code lines — blanks and comments excluded)
**per directory class**; the theme's LOC is charged ONCE, at its actual
wave total (600 of the 650 cap — measured by the factory's gate-2 command,
`git diff 20653bb...f350f7a --numstat` net over `packages/theme/**` +
template `src/static` + `ops/gallery-build.mjs`; this repo's `vendor/theme`
is a byte-copy and adds zero).

## Rebuild LOC by class

| Class | Files | cloc code | Raw lines | What it is |
| --- | --- | --- | --- | --- |
| Pack data (sitefactory `packs/practice/`) | 2 | 38 | 132 | `pack.jinja` (24) + `admin/practice.yml` (14) — the whole vertical's schema/admin declaration |
| Content YAML (`src/content/`) | 65 | 469 | 472 | 64 entity entries + 1 markdown page — every fact exactly once |
| `site.config.yaml` | 1 | 129 | 173 | identity, preset, discovery, budgets, deploy, linkcheck policy + composed `home:` (10 anchored sections) + `pages:` referrals |
| Scratch glue | 5 | 69 | 102 | `robots.txt` (3 code) + `dr-kara-hokes.vcf` (12, raw — no cloc lexer) + `vcf-sync.yml` (41 code) + emitted-file deltas counted code-only (13): DiscoveryHead og:image + a11y token override (+23/−5 raw) and the composed-pages route head-extra mount (+9/−0 raw) |
| Theme (counted once, factory wave total) | — | 600 | 600 | A1 faq/cards 164 + A2 email/badge/meter 74 + B1a variant layer 104 + B1b 8 variants 52 + B2 composed home 129 + B3 jsonld/print 77 |
| **Total** | **73** | **1,305** | **1,479** | |

Excluded from both sides (symmetric): docs/README/AGENTS prose (the bench's
~203 docs lines are outside its 6,323 too), lockfiles, generated build
output, binary assets. Also excluded from the rebuild: the copier-emitted
scaffold (`src/pages/index.astro`, emitted CI workflow, gates spec,
admin shell, …) — template output amortized across every site in every
pack, of which only the theme slice is charged here (600, once). No bench
binaries are shipped at all (portrait/book covers are practice-owned).

## Ratio

```
base    = 6,323 − 1,850 comparable (form 497 + worker 279 + JS tools 502 +
          scrollspy/reveals/back-to-top/next-section 268 + print sheet 121 +
          analytics 61 + header JS share ~130 — OUT by design, replaced by
          static surfaces; see the README parity table)
        = 4,473 LOC
rebuild = 1,305 LOC (cloc code, theme at actual 600)

ratio   = 4,473 / 1,305 = 3.43x   ← target: beat 3.4x — MET
```

### Sensitivities (stated, not hidden)

- Theme at its 650 CAP instead of actual 600: rebuild 1,355 → **3.30x**.
- Raw-line counting on the rebuild side (1,479 raw incl. blanks/comments;
  base stays at its raw 4,473): **3.02x**. The pinned method pairs the
  base's raw count with the cloc-code rebuild — the same accounting the
  gap analysis used for its ~1,325 LOC projection ("≈3.4x"); the measured
  rebuild lands just under that projection (the a11y token override, the
  composed-page head mount, and the linkcheck policy cost 17 code lines —
  see the parity table and the README practice notes).

Reconciliation of the base (re-measured for this doc, raw `wc`):
`bench-karahokes/src` = 6,044 lines (incl. 266 CSS, incl. the 1,210-LOC
shadcn primitives layer) + `worker/` = 279 → **6,323**, exactly the gap
analysis figure.

## Where the difference comes from

- 12 hand-built section components (1,673 LOC of section TSX) → 1 pack
  declaration + block kinds + a composed-home config bound to entity YAML.
- The 1,210-LOC shadcn UI-primitive layer + upgrade treadmill → the vendored
  theme's token sheet (9 presets, this site uses `clinical-calm`).
- Content in TSX (the bench's CI-enforced triple-sync `site.ts ↔ vcf ↔
  worker` and "answers mirror copy" engineering) → each fact in exactly one
  YAML entry; the FAQPage JSON-LD renders from the same entries as the faq
  block, and the vCard sync is one grep (`vcf-sync.yml`).
- 279 LOC of Worker backend + 497 LOC of form UI → OUT by design; the
  shipped surface is the tel/mailto contact card (HIPAA-adjacent intake
  stays out).

Dependencies: 18 runtime (next, react, 7× radix, react-hook-form,
@hookform/resolvers, zod, framer-motion, lucide-react, cva, clsx,
tailwind-merge) → 4 declared (astro, @astrojs/sitemap, @astrojs/rss,
@moliver28/theme — vendored `file:` dep). Runtime client JS shipped by the
rebuild: **0 bytes** (the bench ships the full React island set).

## Reproduce

```sh
npx cloc --quiet --yaml \
  ../sitefactory/templates/journalism/packs/practice \
  src/content site.config.yaml static/robots.txt \
  static/dr-kara-hokes.vcf .github/workflows/vcf-sync.yml
# theme slice (factory-side): git diff 20653bb...f350f7a --numstat -- packages/theme 'templates/journalism/template/src' 'templates/journalism/template/static' ops/gallery-build.mjs  # net, excl test/
# bench base (raw): find ../bench-karahokes/src ../bench-karahokes/worker -type f \( -name '*.ts' -o -name '*.tsx' -o -name '*.css' -o -name '*.sql' \) -exec cat {} + | wc -l
```
