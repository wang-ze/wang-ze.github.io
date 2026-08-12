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

Ze maintains multiple tailored resume variants (e.g., role-specific ones); **none of those go on the public site**. A single general CV PDF (`WangZe_CV_2026.3.pdf`) *is* committed at the repo root and linked via a "My CV" button at the bottom of [index.qmd](index.qmd); Ze refreshes that file manually and the link expects that exact filename.

## Layout

- [_quarto.yml](_quarto.yml) — site config (theme, footer, format defaults). The navbar is **logo-only** — no About/Projects/Books tabs, and no social icons either.
The social links (LinkedIn/GitHub/Scholar/ORCID/ResearchGate) live *only* in the `about: trestles` header of [index.qmd](index.qmd); they used to be duplicated in the navbar, which made every icon appear twice on the page. Don't reintroduce them there.
The page footer is `&copy; Ze Wang` only — the default "Built with Quarto" credit was deliberately removed.
- [index.qmd](index.qmd) — the **single landing page** (Quarto `about: trestles` template; uses `profile.jpg` at repo root, which is committed). Everything lives here: bio, experience, education, certifications, selected publications, and inline **Books** and **Selected projects** sections. The old standalone `projects/index.qmd` and `books/index.qmd` listing pages were **deleted** when this content was folded into the landing page.
- [projects/](projects/) — only the per-project subdirectories remain (`projects/flipped-math-study/`, `projects/latinx-workers/`). They're no longer rendered as a Quarto listing grid; the live project blurbs are written inline in [index.qmd](index.qmd). The subdirs are kept on disk but are effectively orphaned.
- [books/](books/) — the committed rendered HTML for each book at `books/quant-foundations/` and `books/sem/` (served at `https://wang-ze.github.io/books/<slug>/`). Sources live at `books/<slug>-src/` (gitignored) — see "Book workflow" below. There is no longer a `books/index.qmd` landing page; the book blurbs are inline in [index.qmd](index.qmd).
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

**Add a project / book entry:** edit the inline `## Selected projects` or `## Books` section in [index.qmd](index.qmd) directly — add a `###` heading linking out plus a short blurb, matching the existing entries. (There is no auto-listing anymore; a Quarto `listing:` block would conflict with the page's `about: trestles` template.)

**Swap themes:** edit the `theme:` line in [_quarto.yml](_quarto.yml). Bootswatch options: `cosmo`, `flatly`, `litera`, `journal`, `lumen`, `sandstone`, `simplex`, `spacelab`, `united`, `yeti`, `zephyr`, `minty`, `sketchy`.

## Book workflow

The two open-access books (`quant-foundations` and `sem`) follow a "source local, rendered pushed" pattern:

- **Sources** live at `books/quant-foundations-src/` and `books/sem-src/`. These directories are gitignored (`/books/*-src/` in [.gitignore](.gitignore)) so they never leave Ze's machine.
- **Rendered HTML** lives at `books/quant-foundations/` and `books/sem/`. These are committed; they're what gets served at `https://wang-ze.github.io/books/<slug>/`.

> **Sources recovered, ported to Quarto, and rendered.** The Bookdown `.Rmd` sources were found at `/Users/ze/Documents/projects/books/` and ported to `.qmd` Quarto book projects under `books/quant-foundations-src/` and `books/sem-src/` (each with a `_quarto.yml` setting `output-dir: ../<slug>/`). These `-src/` dirs are gitignored; only the rendered HTML at `books/<slug>/` is committed. **Both books have now been re-rendered natively from these Quarto sources** — the committed `books/<slug>/` HTML is current (no longer the old pre-rendered `_book/` output). (xaringan slide sources for `quant-foundations` were likewise brought over to `slides/quant-foundations-src/`, also gitignored.)

> **Per-chapter knitr setup via `_common.R`.** Quarto renders each chapter `.qmd` in its **own** R session, so chunk-option templates and `opts_chunk` defaults set only in `index.qmd` do **not** carry into the chapters. Each `books/<slug>-src/` therefore has a `_common.R` (the single source of truth for `knitr::opts_chunk$set(...)` and the `opts_template$set(...)` definitions — `no.message`, `fig.large`, `fig.small`), sourced from a hidden, **labeled** setup chunk at the top of every chapter and from `index.qmd`:
> ````r
> ```{r}
> #| label: setup-common
> #| include: false
> source("_common.R")
> ```
> ````
> The label matters: an *unlabeled* setup chunk shifts knitr's `unnamed-chunk-N` counter and renames every figure file on render. To suppress R package-load output, library() chunks carry explicit `message=FALSE, warning=FALSE` (don't rely on the `no.message` template alone for those). If you add a chapter, give it the same labeled `source("_common.R")` chunk first.

> **Lecture-slide links point to the old Netlify site, not the slides rendered in this repo.** In the `## Books` section of [index.qmd](index.qmd) the "Lecture slides →" links for both books resolve to the previous site hosted on Netlify (`https://zewang.netlify.app/slides/<slug>/#1`), *not* to local `/slides/<slug>/` paths in this project. This is deliberate: the canonical, working slide decks live on the Netlify site. Don't "fix" these to point at `/slides/...` here unless the slides have actually been rendered and committed into this repo. The "Previous site →" book links similarly point at the Netlify `_book/` output.

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

**R dependencies.** Both books execute R on render. `quant-foundations` runs all its R chunks; `sem` runs its R/`lavaan` chunks too — only the Mplus pieces are static (the `mplus` blocks are display-only, and the handful of R chunks that read Mplus `.out` files are individually marked `eval: false`, since Mplus is Windows-only and those `.out` files don't exist here). Each `books/<slug>-src/` has an `install-packages.R` listing the exact CRAN packages; run `Rscript install-packages.R` once before rendering that book. Caveat: `tidyverse` and `kableExtra` (QF) pull in `ragg`/`svglite` → `textshaping`/`systemfonts`, which need native libs — `brew install harfbuzz fribidi pkg-config libtiff webp freetype libpng jpeg-turbo` on macOS — to build; everything else installs without them. (These are installed on Ze's machine; the full QF package set is in place.)

## Deployment (one-time setup)

After first push to GitHub:

1. The GH Actions workflow renders on `main` and force-pushes the rendered site to a `gh-pages` branch.
2. In the repo's **Settings → Pages**, set **Source** to "Deploy from a branch", **Branch** to `gh-pages`, folder `/ (root)`. Save.
3. The site comes online at `https://wang-ze.github.io/` within a minute or two.

If GH Actions fails on the first run because the `gh-pages` branch doesn't exist yet, run the workflow manually from the **Actions** tab — it will create the branch.

## Pending work / gotchas

- **Book migration — done.** Both Bookdown books were ported `.Rmd` → `.qmd` and natively re-rendered in Quarto; the committed `books/<slug>/` HTML is current. (See "Book workflow" above for the render/setup details.)
- **`profile.jpg`.** Checked in at repo root (downsized copy of the old site's `Ze Wang.jpg`). The `about: trestles` header in [index.qmd](index.qmd) references it.
- **`WangZe_CV_2026.3.pdf`.** Committed at the repo root and linked via the "My CV" button at the bottom of [index.qmd](index.qmd). Ze updates this file manually; the link expects that exact filename, so bump the link if the filename changes.
- **Custom domain.** Not configured. If/when Ze adds one, drop a `CNAME` file at the repo root with the bare domain and configure DNS.
