# TechyTok!

Source of https://techytok.com: Aurelio Amerio's blog and the "From zero to Julia" course.

The site is built with Jekyll on the [Minimal Mistakes](https://github.com/mmistakes/minimal-mistakes)
theme, pulled in at build time as a pinned remote theme
(`remote_theme: "mmistakes/minimal-mistakes@4.28.1"` in `_config.yml`). The theme's own files
are not in this repository; only content and a few override files are.

## Local preview

```bash
bundle install
bundle exec jekyll serve
```

Then open http://127.0.0.1:4000. Add `--drafts` to include `_drafts/`. The `Gemfile` uses the
`github-pages` gem, so the local build runs the same Jekyll version and plugins as GitHub Pages.

## Deployment

GitHub Pages builds and serves the `master` branch (custom domain in `CNAME`). Pushing to
`master` publishes the site.

## Override files

Any file in this repository at the same path as a theme file replaces the theme's copy.
These are the overrides:

| File | What it carries | Basis |
|---|---|---|
| `_includes/head/custom.html` | MathJax, favicon links | Supported hook. |
| `_includes/footer/custom.html` | "Terms & policies" and "Sitemap" links | Supported hook. |
| `_includes/archive-single.html` | Teaser image in list mode; full excerpt + "Read more" | Theme file with the list-mode edits re-applied. |
| `_includes/archive-single-plain.html` | Stock card used by related posts and the course page | Exact copy of the theme's `archive-single.html` under this name. |
| `_includes/page__related.html` | Related posts grid, four items, uses the plain card | Theme file with one include name changed. |
| `_includes/post_collection.html` | Course landing page article grid | Site file. |
| `_layouts/single.html` | "Acknowledgements" section after the body | Theme file with the section re-applied. |
| `assets/css/main.scss` | Theme imports plus `add_line_numbers`, list-card title spacing | Theme file plus one import and one rule. |
| `_sass/_add_line_numbers.scss` | Line-number styling for code blocks | Site file. |
| `assets/js/add_line_number.js` | Line-number script | Site file, loaded via `after_footer_scripts` in `_config.yml`. |
| `_data/ui-text.yml` | UI strings | Verbatim theme file (GitHub Pages does not load theme data files). |
| `_data/navigation.yml` | Main nav and `zero-to-julia` sidebar | Site file. |
| `_data/from_zero_to_julia.json` | Course article list | Site file. |

## Yearly sync procedure

1. Check https://github.com/mmistakes/minimal-mistakes/releases for the new version.
2. In a branch, change the version in `remote_theme`, run `bundle update`, rebuild.
3. For each theme-derived override file in the table above, diff upstream old-version vs
   new-version for that path and re-apply any upstream change on top of the override:

   ```bash
   git fetch https://github.com/mmistakes/minimal-mistakes.git 'refs/tags/*:refs/tags/*'
   git diff 4.28.1 <new-tag> -- _includes/archive-single.html _includes/page__related.html _layouts/single.html assets/css/main.scss _data/ui-text.yml _includes/head/custom.html _includes/footer/custom.html
   ```

   `_data/ui-text.yml` and `_includes/archive-single-plain.html` are plain copies; refresh them with
   `git show <new-tag>:_data/ui-text.yml > _data/ui-text.yml` and
   `git show <new-tag>:_includes/archive-single.html > _includes/archive-single-plain.html`.
4. Preview with `bundle exec jekyll serve`, compare with the live site, merge to `master`.
