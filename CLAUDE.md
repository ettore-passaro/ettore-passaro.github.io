# CLAUDE.md

Portfolio site on GitHub Pages, [Minimal Mistakes](https://mmistakes.github.io/minimal-mistakes/) remote theme (not vendored — no local `_layouts/`).

## Commands

- Ruby via rbenv: run `eval "$(rbenv init - zsh)"` first if not initialised
- `bundle config set path './vendor/bundle' && bundle install`
- `bundle exec jekyll serve --livereload` / `bundle exec jekyll build`
- Deploy: push to `master` (GitHub Actions)

## Content conventions

- `_posts/`: `categories:` must be exactly one value — `Portfolio` (case studies) or `Blog` (blog posts). Drives the URL (`/:categories/:title/`) and which listing page it appears on. Use `tags:` for anything else.
- `_pages/portfolio.md`, `blog.md`: `layout: category` + `taxonomy: <value>`.

## Local theme overrides

Intentional forks, not stray copies — diff against upstream when the theme updates.

- `assets/css/main.scss` — required scaffold; without it local `_sass/` is ignored.
- `_sass/_overrides.scss` — all custom CSS.
- `_includes/archive-single.html` — blurred-teaser backdrop (needs the inline `background-image` here + the `::before` blur in `_overrides.scss`, both parts required), 255-char excerpt truncation (was 160).
- `_includes/page__meta.html` — adds the `.archive__item-type` category badge.
- `_includes/posts-category.html` — fixes an upstream bug: forwards `type=` so `layout: category` pages respect `entries_layout: grid`.

## Rules

- No raw HTML in markdown — use `_includes/` + `{% include %}`.
- Images must be WebP (`cwebp input -o output.webp`).
