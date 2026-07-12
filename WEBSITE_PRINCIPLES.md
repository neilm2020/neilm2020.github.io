# Website Architecture Playbook

This document records how this site is built and why, for future (AI-assisted)
maintenance. It follows the build specification at
<https://gking.harvard.edu/mysite/files/ACADEMIC_SITE_PROMPT.md>; read that
first when making structural changes.

## Stack

| Layer | Choice |
|-------|--------|
| Generator | Hugo extended (pinned in CI: see `HUGO_VERSION` in `.github/workflows/deploy.yml`) |
| Theme | Hugo Blox `blox-tailwind` v0.10.0, vendored in `_vendor/` via `hugo mod vendor` |
| CSS | Tailwind v4 (via theme + npm `@tailwindcss/cli`) + `assets/css/custom.css` for all project styles |
| Search | Pagefind (WASM, static), built in CI after Hugo, loaded lazily by the search modal |
| Hosting | GitHub Pages project site, deployed by GitHub Actions on every push to `main` |

Local build: `npm install` once, then
`hugo server` (dev; search falls back to Google) or
`hugo --gc --minify && npx pagefind --site public` (production).

## Design

The visual model is <https://j-hai.github.io/> (al-folio): white background,
near-black text, hairline dividers, generous whitespace, dense single-line
citations. The accent is Stanford cardinal `#8c1515` (also the old site's link
color), used only for links, active tabs, buttons, and the monogram favicon.
All tokens are CSS custom properties at the top of `assets/css/custom.css`.
The site is forced light mode. A custom Hugo Blox color theme lives at
`assets/css/themes/cardinal.css` (`appearance.color: cardinal` in `hugo.yaml`).

## Content model

- Every publication is a directory `content/publication/<slug>/index.md`.
  **The directory name is the permanent URL slug — never rename it.**
- Structured citation fields: `authors`, `et_al` (adds ", et al." — used for the
  four large-team Facebook/Instagram Election Study papers, matching how the
  C.V. cites them), `editor` (renders "(editor)"), `journal`, `volume`,
  `number`, `pages`, `publisher` (books), `doi`, `links`, `tags`.
- `tags` carry the research-area name; `data/research_areas.json` maps each of
  the five areas to its featured publication slugs. The homepage accordions and
  the Writings page's area filter both read that file.
- Books use `publication_types: ["book"]` and a `featured.jpg` cover in the
  bundle (auto-discovered by templates).
- The site intentionally features only representative works (per the owner's
  information form). The complete list lives in the C.V. PDF.

## Key templates (all in `layouts/`)

| File | Role |
|------|------|
| `home.html` | Hero (photo left / text right, stacked on mobile) + Books + research-area accordions (`<details>`, no JS). Anchors `#books`, `#research-areas` have `scroll-margin-top`. |
| `publication/list.html` | The Writings page: server-rendered citation rows + vanilla-JS tabs (All/Articles/Books), text search, area dropdown, year checkboxes, sort, live count, BibTeX download of visible rows, hash routing (`#book`, `#area=<slug>`). |
| `publication/single.html` | Citation header, cover, link buttons, See Also. Publication-type labels are deliberately not shown. |
| `people/single.html` | Auto-collects co-authors from all `authors:` fields (owner excluded), alphabetizes by last name, links via `data/coauthors.json`. Names-only presentation (solo researcher, no lab). |
| `_partials/citation_row.html` | Single source of the dense citation line (owner's name bolded; books italic + publisher, articles quoted + journal). |
| `_partials/related_finder.html` | Build-time "See Also": explicit `related_*` overrides win, then scoring (+2 shared title token, +1 shared author last name, +2 shared tag), threshold 4, cap 8, title-dedup. |
| `_partials/components/headers/navbar.html` | Brand = owner's name (links home), nav right, search = icon only, CSS-checkbox hamburger on mobile. |
| `_partials/components/search-modal.html` | Pagefind modal, `Ctrl/Cmd-K`, lazy import via `relURL`, Google `site:` fallback. |
| `_partials/site_footer.html` | Footer; "Created using GaryKing.org/mysite" credit gated by `params.mysite.credit` (currently **false**). |
| `_partials/hooks/head-end/mysite.html` | favicon.ico link + discovery metadata (`meta generator` + homepage JSON-LD Person) gated by `params.mysite.discovery` (currently **false**). |
| `_shortcodes/staticrel.html` | Subpath-safe static URLs in content: `{{</* staticrel "files/cv.pdf" */>}}`. |

## Subpath safety (GitHub Pages project site)

The site lives under `/<repo>/`, so absolute paths starting with `/` break.
Rules: use `relURL` **without** a leading slash in templates; use the
`staticrel` shortcode in Markdown; never lead `links:` URLs with `/`. CI passes
`--baseURL` from `configure-pages`, so the deployed base path is always right
even if the repo is renamed or moved to a user site.

## Things future maintainers must not do

- Don't rename publication directories (URLs are permanent).
- Don't put the same text on the homepage intro and the Bio page.
- Don't show `publication_types` labels on publication pages.
- Don't add process/sourcing commentary to any rendered text — flags go in
  HTML comments.
- Don't hand-maintain related links or the co-author list; they are computed.
  (Exception: `data/coauthors.json` URLs are hand-curated — verify before adding.)
- Don't bypass the theme override convention: copy from
  `_vendor/.../layouts/_partials/<path>` to `layouts/_partials/<path>` and edit
  the copy.

## Known flags from the initial build (July 2026)

- The information form described González-Bailón et al. (2023) as a *Nature*
  article; it appeared in *Science* (verified via Crossref and the C.V.). The
  site links the Science DOI; a comment sits in that page's Markdown.
- Co-author links in `data/coauthors.json` were resolved by web search and
  should be reviewed by the owner (see UPDATING.md). Ferenstein, McConnell,
  and Franco are unlinked (no page could be verified).
- The old site's bio contained the typo "Rosenbrg", fixed to "Rosenberg" here.
