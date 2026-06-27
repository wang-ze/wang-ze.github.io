# CLAUDE.md

Guidance for Claude Code when working in this repository.

## What this repo is

Ze Wang's personal site — the **new** site, built with **Quarto** and deployed to **GitHub Pages** at `https://wang-ze.github.io/`. Replaces the older Hugo + blogdown site at [/Users/ze/projects/website](../website) (still on disk, no longer maintained).

Stack:

- [Quarto](https://quarto.org) (verified 1.9+). Markdown + `.qmd` content, Python/Jupyter where useful.
- Theme: Bootswatch `cosmo` (set in [_quarto.yml](_quarto.yml)). Easy to swap (`flatly`, `litera`, `journal`, etc.) by editing one line — Ze wanted to compare side-by-side before committing.
- GitHub Pages deploy via [.github/workflows/publish.yml](.github/workflows/publish.yml) (renders on `main`, publishes to `gh-pages` branch).

Repo is a "user site" (`wang-ze.github.io`), so the URL has no `/<repo-name>/` prefix.

## Owner context

Ze is a **Research Scientist at AWS (Ashburn, VA) since June 2022**. Previously tenured Associate Professor of Statistics, Measurement, and Evaluation in Education at the University of Missouri (2008–2022). Site copy in [index.qmd](index.qmd) reflects this — don't reintroduce the old academic-only framing.

Ze maintains multiple tailored resume variants (e.g., role-specific ones); **none of those go on the public site**. The site doesn't host a CV PDF download by design — bio content is on-page only.

## Layout

- [_quarto.yml](_quarto.yml) — site config (navbar, theme, footer, format defaults).
- [index.qmd](index.qmd) — landing / about page (Quarto `about: trestles` template; expects `profile.jpg` at repo root, not yet checked in).
- [projects/](projects/) — portfolio. `projects/index.qmd` renders cards via Quarto's listing grid; each project lives in its own subdirectory (`projects/<slug>/index.qmd`).
- [books/](books/) — landing page for the two open-access books, plus the rendered HTML for each book at `books/quant-foundations/` and `books/sem/` (committed). **Source files for both books are currently missing — see "Book workflow" below.** When sources are recovered, they live at `books/<slug>-src/` (gitignored); only the rendered HTML at `books/<slug>/` is committed and pushed.
- [styles.css](styles.css) — small set of overrides on top of the chosen theme. Keep it small; prefer theme changes in `_quarto.yml`.
- [.github/workflows/publish.yml](.github/workflows/publish.yml) — Quarto + GH Pages deploy via `quarto-dev/quarto-actions`.
- `_site/`, `_freeze/`, `.quarto/` — build output and cache (gitignored).

## Common tasks

**Preview locally:**
```bash
quarto preview
```

**One-shot build:**
```bash
quarto render
```

**Add a project entry:**
```bash
mkdir projects/<slug>
$EDITOR projects/<slug>/index.qmd
```
Frontmatter must include `title`, `date`, ideally `description` and `categories`. The listing page picks these up automatically.

**Swap themes:** edit the `theme:` line in [_quarto.yml](_quarto.yml). Bootswatch options: `cosmo`, `flatly`, `litera`, `journal`, `lumen`, `sandstone`, `simplex`, `spacelab`, `united`, `yeti`, `zephyr`, `minty`, `sketchy`.

## Book workflow

The two open-access books (`quant-foundations` and `sem`) follow a "source local, rendered pushed" pattern:

- **Sources** live at `books/quant-foundations-src/` and `books/sem-src/`. These directories are gitignored (`/books/*-src/` in [.gitignore](.gitignore)) so they never leave Ze's machine.
- **Rendered HTML** lives at `books/quant-foundations/` and `books/sem/`. These are committed; they're what gets served at `https://wang-ze.github.io/books/<slug>/`.

> **Sources recovered and ported to Quarto.** The Bookdown `.Rmd` sources were found at `/Users/ze/Documents/projects/books/` and copied + ported to `.qmd` Quarto book projects under `books/quant-foundations-src/` and `books/sem-src/` (each with a `_quarto.yml` setting `output-dir: ../<slug>/`). These `-src/` dirs are gitignored; only the rendered HTML at `books/<slug>/` is committed. The committed HTML still reflects the old pre-rendered `_book/` output until the books are re-rendered from the ported sources. (xaringan slide sources for `quant-foundations` were likewise brought over to `slides/quant-foundations-src/`, also gitignored.)

Each source directory is its own Quarto book project. Its `_quarto.yml` should set:

```yaml
project:
  type: book
  output-dir: ../<slug>/   # writes rendered HTML into the committed path
```

Render workflow:

```bash
cd books/<slug>-src/
quarto render
# rendered HTML now sits at books/<slug>/
git add books/<slug>/
git commit -m "Render <slug>"
git push
```

The site-level `quarto render` (run by CI on every push to `main`) treats the pre-rendered book HTML as static and passes it through unchanged — CI never sees or re-renders the book sources. This is intentional: book content updates are deliberate, not on every site rebuild.

When opening this repo cold, the `books/<slug>-src/` directories may not exist locally yet — Ze keeps them on the machine where book editing happens.

**R dependencies.** `quant-foundations` executes its R chunks on render; `sem` is set `execute: eval: false` in its `_quarto.yml` (its Mplus chunks ran a Windows-only Mplus binary, so code is shown but not executed — the committed `books/sem/` HTML preserves the original output). Each `books/<slug>-src/` has an `install-packages.R` listing the exact CRAN packages; run `Rscript install-packages.R` once before rendering that book. Caveat: `tidyverse` and `kableExtra` (QF) pull in `ragg`/`svglite` → `textshaping`/`systemfonts`, which need native libs — `brew install harfbuzz fribidi pkg-config libtiff webp freetype libpng jpeg-turbo` on macOS — to build; everything else installs without them. (These are installed on Ze's machine; the full QF package set is in place.)

## Deployment (one-time setup)

After first push to GitHub:

1. The GH Actions workflow renders on `main` and force-pushes the rendered site to a `gh-pages` branch.
2. In the repo's **Settings → Pages**, set **Source** to "Deploy from a branch", **Branch** to `gh-pages`, folder `/ (root)`. Save.
3. The site comes online at `https://wang-ze.github.io/` within a minute or two.

If GH Actions fails on the first run because the `gh-pages` branch doesn't exist yet, run the workflow manually from the **Actions** tab — it will create the branch.

## Pending work / gotchas

- **Book migration.** The Bookdown sources for `quant-foundations` and `sem` live in the old repo at `/Users/ze/projects/website/content/books/`. The user picked "re-render natively in Quarto" — i.e., port `.Rmd` → `.qmd` for both books, not copy the pre-rendered HTML. This was deferred from initial scaffolding because it's a non-trivial multi-file pass; the `books/index.qmd` placeholder links to the old site until then. R toolchain may or may not be needed depending on whether code chunks need to execute.
- **`profile.jpg`.** Already checked in at repo root (downsized copy of the old site's `Ze Wang.jpg`). The about page in [index.qmd](index.qmd) references it.
- **`cv.pdf`.** Linked as a download from the "Selected publications" section in [index.qmd](index.qmd). The file lives at the repo root (`cv.pdf`). Ze copies the latest version of the CV there manually; the link expects that exact filename.
- **Custom domain.** Not configured. If/when Ze adds one, drop a `CNAME` file at the repo root with the bare domain and configure DNS.
