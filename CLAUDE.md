# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

The source for https://techytok.com — Aurelio Amerio's personal blog and the "From zero to Julia" tutorial series. It is a Jekyll site based on a vendored copy of the Minimal Mistakes theme (v4.17.2): the theme's `_includes/`, `_layouts/`, `_sass/`, and `assets/` live directly in this repo and have been customized, while `_config.yml` also points at `remote_theme: mmistakes/minimal-mistakes`. Deployment is GitHub Pages from `master` (CNAME → techytok.com); there is no CI.

## Commands

- `bundle install` — install Ruby dependencies (Gemfile delegates to the theme gemspec).
- `bundle exec jekyll serve` — build and serve locally; add `--drafts` to include `_drafts/`.
- `npm run build:js` — only needed when editing files under `assets/js/`; concatenates/uglifies plugins into `assets/js/main.min.js` and re-adds the banner. Editing `main.min.js` directly is pointless — it gets overwritten.
- The `Rakefile` (`rake preview`) is a leftover from theme development and targets a `test/` directory; it is not how this site is previewed.

There are no tests or linters.

## Content architecture

- `_posts/` — blog posts and lessons, named `YYYY-MM-DD-slug.md`. Every post sets an explicit `permalink:` (e.g. `/lesson-functions/`), so URLs are decoupled from filenames. Header/teaser images conventionally live in `assets/images/YYYY/MM/DD/`. Defaults in `_config.yml` already give posts the `single` layout, author profile, Disqus comments, read time, and sharing — don't repeat those in front matter.
- `_pages/` — static pages (about, contact, 404, archives), each with an explicit `permalink`.
- `_drafts/` — unpublished drafts.

### "From zero to Julia" course

Lessons are ordinary posts wired into the course in two extra places — adding or renaming a lesson means touching all of them:

1. The post itself, with `sidebar: nav: "zero-to-julia"` in its front matter so the course sidebar shows.
2. `_data/navigation.yml` — the `zero-to-julia` nav list (lesson order and titles in the sidebar).
3. `_data/from_zero_to_julia.json` — the article list consumed by `_pages/from-zero-to-julia.md` (the course landing page).

Some lessons link Colab notebooks stored under `assets/colab/`.

## Site configuration

`_config.yml` holds all site settings (Disqus, Algolia/lunr search, analytics, author profile in combination with `_data/author.yml`). It is not hot-reloaded — restart `jekyll serve` after changing it.
