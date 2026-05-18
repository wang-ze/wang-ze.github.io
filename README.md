# wang-ze.github.io

Personal site of Ze Wang — Research Scientist at AWS. Built with [Quarto](https://quarto.org), deployed via GitHub Pages.

Live site: https://wang-ze.github.io

## Local preview

```bash
quarto preview
```

Quarto 1.9+ recommended.

## Build

```bash
quarto render
```

Output goes to `_site/` (gitignored). On push to `main`, GitHub Actions renders and publishes to the `gh-pages` branch (see [.github/workflows/publish.yml](.github/workflows/publish.yml)).

## Structure

- `index.qmd` — landing / about page
- `posts/` — blog
- `projects/` — portfolio
- `books/` — open-access books (migration in progress)
- `_quarto.yml` — site config
- `styles.css` — small set of overrides on top of the theme
