# CLAUDE.md

This file provides guidance for AI assistants (Claude Code, opencode, etc.) when working with code
in this repository.

## What this repository is

A 12-module, video-first Arabic ML bootcamp companion repo. Each module pairs a YouTube lecture
playlist with written material — Jupyter notebooks (`CODE/`), theory PDFs, and notes. The published
content is notebooks, Python scripts, datasets, and PDFs, plus a self-contained `NN_reading.html`
per module and a single `index.html` landing page that links the whole course together. This site
layer was added to mirror the structure of an existing SQL-for-data-analysis course repo: a static,
self-contained HTML "site" with a shared design system, a fixed sidebar navigating all 12 modules,
and GitHub Pages hosting (see `.nojekyll`).

## Site structure (the HTML layer)

Everything renders as plain static HTML opened directly in a browser — no build step, no server, no
package.json. GitHub Pages serves the repo at the site root via `.nojekyll`.

- `index.html` — the landing page. Fixed left sidebar + module cards (one per module), dark/light
  theme toggle. The sidebar appears on every page; the home entry is `index.html`.
- Each module folder contains one `NN_reading.html` (NN = zero-padded module number, e.g.
  `01_reading.html`) that mirrors the lecture topics as written concepts, and a `README.md` with the
  full video table (YouTube links, durations, PDF links, CODE links).
- The existing notebooks, PDFs, and CSVs inside each module are untouched by the site layer — the
  reading HTMLs link out to them (and to GitHub) rather than duplicating them.

## Reading page format

Each `NN_reading.html` is one self-contained file — no external CSS/JS beyond Google Fonts. Copy
the shared `<style>` block and the sidebar/theme-toggle boilerplate from the most recently built
page rather than reinventing it.

- **Design system**: fonts are Bricolage Grotesque (headings), Nunito Sans (body), IBM Plex Mono
  (code/labels) via Google Fonts `<link>` tags. Color tokens: ink `#141414`, blue `#2F63E8` /
  blue-dark `#1E4BC4`, gray `#5B6472` / gray-muted `#9CA3AF`, panel `#F3F4F6`, border `#E5E7EB`,
  paper background `#FAFBFC`. Code blocks use VS-Code-dark colors: background `#1E1E1E`, default
  text `#D4D4D4` (**must** be set explicitly on `.code-body`), keyword `#569CD6`, function
  `#DCDCAA`, string `#CE9178`, comment `#6A9955`. Component classes: `.objectives`, `.callout`
  with `.tip`/`.insight`/`.warn`/`.recap` variants, `.code-block` + `.code-header` + `.code-body` +
  `.annotations`, `.compare` with `.good`/`.bad` columns, `.card-grid` (2- or 3-column), `figure`/
  `figcaption`, `.day-links`, `.day-nav` (prev/next navigation cards linking Home ↔ next module).
- **Diagrams**: hand-drawn inline SVG on a shared white + subtle grid background, referencing
  `<pattern id="erd-grid">` (`#e6edf3` 1px grid, declared once in the shared `<defs>` near the top
  of `<body>`). Palette: `#333`/`#1f6fb2`/`#dfe3e8`, flat and crisp, no drop shadows, square corners.
  Use `font-family="Segoe UI, Arial, sans-serif"` on the root `<svg>`. No raster images for
  diagrams — vector only. (The SQL course's largest schema ERDs used Mermaid; this bootcamp's
  reading pages don't need full-schema ERDs, so prefer hand-drawn SVG everywhere.)
- **Code blocks**: when illustrating Python, syntax token spans use `.kw`/`.fn`/`.str`/`.com`
  classes from the SQL design system (they match VS-Code-dark colors regardless of language).
- **Keyboard `code.inline`** for single identifiers like `pandas`, `CODE/`, `tweets.csv`.
- **Sidebar**: every reading page includes the full 12-module `<nav class="site-sidebar">` with the
  correct module marked `class="active"`, and `../`-prefixed relative paths back to `index.html` and
  the other module folders.
- **Known bug classes to avoid**:
  - `.code-body` needs an explicit base `color` (the default-text token) — otherwise untokened text
    inherits the page's near-black body text and becomes unreadable against the dark code background.
  - Any literal angle-bracket placeholder used as text (e.g. `<columns>`) must be HTML-escaped as
    `&lt;columns&gt;` — unescaped, it's parsed as a real unknown tag and breaks the DOM.
  - External links to GitHub should use the course repo URL
    `https://github.com/a7madmostafa/Arabic_ML_Bootcamp/tree/main/<module-folder>` with
    `target="_blank" rel="noopener"` and the `#icon-external`/`#icon-download` SVG symbols.
  - Validate a new/edited HTML file with a quick Python `html.parser.HTMLParser` tag-balance check
    (exclude self-closing SVG void elements like `circle`, `line`, `ellipse`, `polygon`, `rect`,
    `path`) before calling it done.

## Structure

- `index.html` — landing page; module cards summarize each module's topics, "what you'll learn,"
  and link to its reading page and GitHub folder.
- `01-intro_to_ai_and_data_science/` … `12-intro_to_nlp/` — 12 module folders, each with:
  - `README.md` — the original per-module table of contents (YouTube video table, PDF/CODE links).
  - `NN_reading.html` — the site layer's written-concepts page (only Module 01 exists so far).
  - `meta.json` — machine-readable module metadata (module number, title, description, video count,
    duration, materials).
  - `CODE/`, `PDFs/`, `data/`, `reports/` — the actual content (notebooks, scripts, datasets,
    theory notes). Not touched by the site layer.
- `memory.md`, `future_improvements.md` — personal progress tracker and backlog (git-ignored).
- `.gitignore` — includes `.html`? No — but it does ignore `memory.md`, `future_improvements.md`.

## Working with this repo

- The HTML site layer is informational/educational — content derived from each module's lecture
  topics and PDFs. When writing a new `NN_reading.html`, source content from the module's own
  `README.md` (topic list) and any PDF in the module; don't invent facts about what a module covers.
- Keep the sidebar in `NN_reading.html` identical to `index.html`'s (same module titles, same hrefs)
  except for the `active` class pointing at the current module.
- Keep `index.html`'s module cards in sync with each module's README topics — the cards summarize
  the same content.
- There is no build/lint/test tooling for the HTML layer; validation is the HTMLParser tag-balance
  check described above. Check `git status` and review the diff before committing.
