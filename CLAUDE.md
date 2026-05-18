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
- [posts/](posts/) — blog. `posts/index.qmd` is the listing; `posts/_metadata.yml` sets per-post defaults; each post lives in its own subdirectory (`posts/<slug>/index.qmd`).
- [projects/](projects/) — portfolio. Same pattern as posts; `projects/index.qmd` renders cards via Quarto's listing grid.
- [books/](books/) — landing page for the two open-access books. **Migration from the old Bookdown sources to native Quarto books is pending** — currently the page links back to the old site.
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

**Add a blog post:**
```bash
mkdir posts/<slug>
$EDITOR posts/<slug>/index.qmd
```
Post frontmatter must include `title`, `date`, ideally `description` and `categories`. The listing page picks these up automatically.

**Swap themes:** edit the `theme:` line in [_quarto.yml](_quarto.yml). Bootswatch options: `cosmo`, `flatly`, `litera`, `journal`, `lumen`, `sandstone`, `simplex`, `spacelab`, `united`, `yeti`, `zephyr`, `minty`, `sketchy`.

## Deployment (one-time setup)

After first push to GitHub:

1. The GH Actions workflow renders on `main` and force-pushes the rendered site to a `gh-pages` branch.
2. In the repo's **Settings → Pages**, set **Source** to "Deploy from a branch", **Branch** to `gh-pages`, folder `/ (root)`. Save.
3. The site comes online at `https://wang-ze.github.io/` within a minute or two.

If GH Actions fails on the first run because the `gh-pages` branch doesn't exist yet, run the workflow manually from the **Actions** tab — it will create the branch.

## Pending work / gotchas

- **Book migration.** The Bookdown sources for `quant-foundations` and `sem` live in the old repo at `/Users/ze/projects/website/content/books/`. The user picked "re-render natively in Quarto" — i.e., port `.Rmd` → `.qmd` for both books, not copy the pre-rendered HTML. This was deferred from initial scaffolding because it's a non-trivial multi-file pass; the `books/index.qmd` placeholder links to the old site until then. R toolchain may or may not be needed depending on whether code chunks need to execute.
- **`profile.jpg`.** Referenced from [index.qmd](index.qmd) but not yet checked in. Drop one at the repo root (the old site has candidates under `../website/static/media/Ze Wang.jpg`).
- **Custom domain.** Not configured. If/when Ze adds one, drop a `CNAME` file at the repo root with the bare domain and configure DNS.
- **No CV PDF.** Intentional — bio content lives on the page. Don't re-add a `cv.pdf` download without checking.
