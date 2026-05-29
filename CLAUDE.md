# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Commands

```bash
# Install dependencies (first time or after Gemfile changes)
bundle install

# Run local development server with live reload
bundle exec jekyll serve --livereload

# Build the site
bundle exec jekyll build

# Run with drafts visible
bundle exec jekyll serve --drafts

# Run Algolia indexing (requires ALGOLIA_API_KEY env var)
bundle exec jekyll algolia
```

**Ruby version:** managed via rbenv (see `.ruby-version`). Run `eval "$(rbenv init - zsh)"` if rbenv is not initialised in the current shell before running any `bundle` or `jekyll` commands.

## Deployment

The site is deployed to GitHub Pages via GitHub Actions (`.github/workflows/deploy.yml`). Pushing to `master` triggers a build and deploy automatically. The legacy GitHub Pages build pipeline is not used.

## Architecture

This is a [Minimal Mistakes](https://mmistakes.github.io/minimal-mistakes/) remote theme site hosted on GitHub Pages, built with Jekyll 4.

**Theme:** `mmistakes/minimal-mistakes` (remote theme via `jekyll-remote-theme`)

**Content directories:**
- `_posts/` — Portfolio case studies (filename: `YYYY-MM-DD-title.md`). Portfolio entries use `categories: [Portfolio]`, which places them at `/portfolio/:title/` via the permalink pattern `/:categories/:title/`.
- `_pages/` — Static pages included via `include: [_pages]` in `_config.yml`

**Key configuration:**
- `_config.yml` — Site identity, author profile, default front matter, permalink structure, plugins
- `_data/navigation.yml` — Top navigation links
- Pagination is set to 5 posts per page (`paginate: 5`)

**Plugins active:** `jekyll-remote-theme`, `jekyll-paginate`, `jekyll-sitemap`, `jekyll-feed`, `jekyll-gist`, `jemoji`, `jekyll-include-cache`, `jekyll-algolia`

## Local overrides

Several theme files are overridden locally. When the theme updates, these should be checked for upstream changes.

**`assets/css/main.scss`** — Required to make SCSS overrides work. Without a local copy of this file, the remote theme's Sass compilation ignores local `_sass/` files entirely. Imports `_sass/_overrides.scss` at the end.

**`_sass/_overrides.scss`** — All local CSS customisations. Appended after the theme's styles so rules here win by cascade. Edit this file for any visual tweaks rather than touching theme files directly.

**`_includes/archive-single.html`** — Copied from the theme with two changes:
  1. Adds `style="background-image: url('{{ teaser }}')"` to the teaser `<div>`, enabling the blurred backdrop effect in CSS.
  2. Increases excerpt truncation from 160 to 255 characters.

  The blurred backdrop is a two-part trick: `_overrides.scss` uses `background: inherit` on the `::before` pseudo-element of `.archive__item-teaser`, but `background: inherit` only copies the parent's `background-image` — it cannot read an `<img>` src. That's why the inline style is needed: it sets the background-image on the container so `::before` can inherit and blur it. Removing either part breaks the effect.

**`_layouts/home.html`** and **`_layouts/posts.html`** — Custom layout overrides.

Minimal Mistakes documentation: https://mmistakes.github.io/minimal-mistakes/docs/configuration/
