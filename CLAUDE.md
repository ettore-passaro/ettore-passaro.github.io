# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Commands

```bash
# Install dependencies (first time or after Gemfile changes)
bundle install

# Run local development server with live reload
bundle exec jekyll serve

# Build the site
bundle exec jekyll build

# Run with drafts visible
bundle exec jekyll serve --drafts

# Run Algolia indexing (requires ALGOLIA_API_KEY env var)
bundle exec jekyll algolia
```

## Architecture

This is a [Minimal Mistakes](https://mmistakes.github.io/minimal-mistakes/) remote theme site hosted on GitHub Pages. All layouts, includes, and SCSS are inherited from the theme — there are no local overrides in this repo.

**Theme:** `mmistakes/minimal-mistakes` (remote theme via `github-pages` gem)

**Content directories:**
- `_posts/` — Blog posts (filename: `YYYY-MM-DD-title.md`)
- `_portfolio/` — Portfolio items (uses `single` layout with author profile)
- `_pages/` — Static pages included via `include: [_pages]` in `_config.yml`

**Key configuration:**
- `_config.yml` — Site identity, author profile, default front matter, permalink structure (`/:categories/:title/`)
- `_data/navigation.yml` — Top navigation links
- Pagination is set to 5 posts per page (`paginate: 5`)

**URL structure:** Posts use `/:categories/:title/` — the category in the filename or front matter affects the URL.

**Plugins active:** `jekyll-paginate`, `jekyll-sitemap`, `jekyll-feed`, `jekyll-gist`, `jemoji`, `jekyll-include-cache`, `jekyll-algolia`

**No custom layouts, includes, or SCSS exist** — to override theme defaults, create matching files in `_layouts/`, `_includes/`, or `assets/css/` locally.

Minimal Mistakes documentation: https://mmistakes.github.io/minimal-mistakes/docs/configuration/
