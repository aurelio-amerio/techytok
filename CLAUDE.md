# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

The source for https://techytok.com — Aurelio Amerio's personal blog and the "From zero to Julia" tutorial series. It is a Jekyll site on the Minimal Mistakes theme, loaded at build time via `remote_theme: "mmistakes/minimal-mistakes@4.28.1"` in `_config.yml` (jekyll-remote-theme, bundled in the `github-pages` gem). The theme's files are **not** vendored: any repo file at the same path as a theme file overrides it. The overrides are:

- `_includes/head/custom.html` (MathJax 4 from jsDelivr, favicons), `_includes/footer/custom.html` (footer links)
- `_includes/archive-single.html` (list cards: teaser image + full excerpt + "Read more"), `_includes/archive-single-plain.html` (verbatim theme card, used by related posts and the course grid), `_includes/page__related.html` (uses the plain card)
- `_includes/post_collection.html` (course landing grid), `_layouts/single.html` (adds the `acknowledgements` front-matter section)
- `assets/css/main.scss` (theme imports + `_sass/_add_line_numbers.scss`), `assets/js/add_line_number.js` (loaded via `after_footer_scripts`)
- `_data/ui-text.yml` (verbatim theme copy; required on GitHub Pages), `_data/navigation.yml`, `_data/from_zero_to_julia.json`

When upgrading the theme, follow the "Yearly sync procedure" in `README.md`: bump the tag, diff upstream for the override paths, re-apply. Never copy other theme files into the repo.

Deployment is GitHub Pages from `master` (CNAME → techytok.com); there is no CI.

## Commands

- `bundle install` — install Ruby dependencies. The `Gemfile` uses the `github-pages` gem (pinned `~> 232`) so local builds match GitHub Pages (Jekyll 3.10); `Gemfile.lock` is committed.
- `bundle exec jekyll serve` — build and serve locally; add `--drafts` to include `_drafts/`. Needs network access to github.com to download the theme.

There are no tests or linters.

## Content architecture

- `_posts/` — blog posts and lessons, named `YYYY-MM-DD-slug.md`. Every post sets an explicit `permalink:` (e.g. `/lesson-functions/`), so URLs are decoupled from filenames. Header/teaser images conventionally live in `assets/images/YYYY/MM/DD/`. Defaults in `_config.yml` already give posts the `single` layout, author profile, Disqus comments, read time, sharing, and related posts (and pages the `single` layout with author profile) — don't repeat those in front matter.
- `_pages/` — static pages (about, contact, 404, archives), each with an explicit `permalink`.
- `_drafts/` — unpublished drafts.

### "From zero to Julia" course

Lessons are ordinary posts wired into the course in two extra places — adding or renaming a lesson means touching all of them:

1. The post itself, with `sidebar: nav: "zero-to-julia"` in its front matter so the course sidebar shows.
2. `_data/navigation.yml` — the `zero-to-julia` nav list (lesson order and titles in the sidebar).
3. `_data/from_zero_to_julia.json` — the article list consumed by `_pages/from-zero-to-julia.md` (the course landing page).

Some lessons link Colab notebooks stored under `assets/colab/`.

## Site configuration

`_config.yml` holds all site settings (Disqus, Algolia/lunr search, analytics, author profile and its links, footer links). It is not hot-reloaded — restart `jekyll serve` after changing it.
