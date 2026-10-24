# Theme upgrade: move techytok.com to a pinned remote Minimal Mistakes theme

Date: 2026-10-24
Status: approved in conversation (owner opted to let Claude settle remaining calls)

## Intent

techytok.com is a Jekyll site built from a vendored fork of the Minimal Mistakes
theme. The fork's real base is theme release **4.17.2** (October 2019). A merge of
4.20.1 in August 2020 was reverted the same day, so git history is misleading: a
plain `git merge` from upstream would silently keep that revert.

The owner wants to focus on writing articles, keep graphical customizations
minimal, and resync with upstream roughly once a year without repeating a
diff-and-reapply exercise. Upstream is now at **4.28.1** (August 2026).

## Goals

- The site renders on the current upstream theme (4.28.1) with all content intact.
- The owner's genuine customizations are preserved (list below).
- A yearly upstream sync becomes a one-line version bump plus a short override check.
- The owner can preview the result locally before anything reaches `master`.
- Nothing is pushed to `master` until the owner has compared the preview with the live site.

## Non-goals

- Redesigning the site, changing skin, fonts, or layout beyond what the theme upgrade implies.
- Rewriting article content or front matter beyond what the new theme requires.
- Adding CI, tests, or a GitHub Actions deploy. GitHub Pages keeps building `master` as today.

## Anti-goals (what would make this a failure)

- Any post, page, image, Colab notebook, or the CV PDF missing from the built site.
- Any URL from the live sitemap (47 entries) that no longer resolves.
- The course sidebar, course landing page, or Disqus comments disappearing.
- Carrying forward dead code or dead services (TinyLetter form, Universal Analytics tag).

## Decisions made by the owner

1. **Approach:** pinned remote theme. `_config.yml` sets
   `remote_theme: "mmistakes/minimal-mistakes@4.28.1"`. The theme's own files are
   deleted from the repo; only overrides and content remain. (Owner asked what this
   means and accepted it: it is a Jekyll build-time download, not a git remote.)
2. **Analytics:** switch to GA4 through the theme's built-in `google-gtag` provider.
   The owner will supply the `G-` measurement ID. Until it arrives, the provider stays
   `false` so no broken script ships.
3. **Newsletter:** remove the TinyLetter form and its stylesheet entirely.
4. **Related posts:** keep the current look (four-card grid with teaser image, title,
   read time, short excerpt), limited to four posts.
5. **Post list cards:** keep the owner's archive-list customization (teaser image in
   list mode, full excerpt followed by a "Read more" link) on top of the 4.28.1 markup.
6. **Ruby/Jekyll:** the owner installs Ruby and Jekyll themselves (needs sudo) after
   the plan is approved and before implementation starts, following
   https://jekyllrb.com/docs/installation/ubuntu/.
7. **Preview:** local `bundle exec jekyll serve` on a branch; the owner compares it with
   the live site and reports graphical differences they dislike before merge.

## Customization inventory (what survives, and where it lives)

Verified by diffing `4.17.2..HEAD` and `4.17.2..4.28.1`.

### Kept as override files (Jekyll layers these over the remote theme)

| File | What it carries | Basis |
|---|---|---|
| `_includes/head/custom.html` | MathJax, favicon links | Supported hook. Analytics snippet removed. |
| `_includes/footer/custom.html` | "Terms & policies" and "Sitemap" links | Supported hook, unchanged. |
| `_includes/archive-single.html` | Teaser image in list mode; full excerpt + "Read more" | Re-applied by hand onto the 4.28.1 file. |
| `_includes/archive-single-plain.html` | Stock card used by related posts and the course page | Exact copy of 4.28.1 `archive-single.html` under this name. |
| `_includes/page__related.html` | Related posts grid, four items, uses the plain card | Copy of 4.28.1 file with one include name changed. |
| `_includes/post_collection.html` | Course landing page article grid | Owner's file, unchanged. |
| `_layouts/single.html` | "Acknowledgements" section after the body | Re-applied by hand onto the 4.28.1 file. |
| `assets/css/main.scss` | Theme imports plus `add_line_numbers` | Copy of 4.28.1 file plus one import line. |
| `_sass/_add_line_numbers.scss` | Line-number styling for code blocks | Owner's file, unchanged. |
| `assets/js/add_line_number.js` | Line-number script | Owner's file; loaded via the theme's `after_footer_scripts` config list instead of a layout override. |
| `_data/ui-text.yml` | UI strings | Required on GitHub Pages (jekyll-data plugin unsupported). Taken verbatim from 4.28.1. |
| `_data/navigation.yml` | Main nav and `zero-to-julia` sidebar | Owner's file; Slapform form ID written directly into the nav URL, which removes the masthead override. |
| `_data/from_zero_to_julia.json` | Course article list | Owner's file, unchanged. |

### Kept as content

`_posts/`, `_pages/`, `_drafts/`, `index.html`, `assets/images/`, `assets/colab/`,
`assets/favicon/`, `assets/cv_and_publications.pdf`, `CNAME`.

### Dropped (upstream 4.28.1 already has it, or it is dead)

- Back-ported fixes: keybase icon color, notices `pre code` fix, page tagline, TOC
  include, date formats, SEO description, Staticman config path, archive hero headers,
  masthead logo URL, `seo.html`, `comments.html`, `comment.html`.
- Trimmed `ui-text.yml` (replaced by the full upstream file).
- `_includes/contact-form.html` (unused; contact page links to the hosted Slapform form).
- `_includes/card.html`, `_sass/cards/` (never imported; stylesheet import is commented out).
- `_sass/tinyletter/`, the TinyLetter form in `footer.html`.
- `_data/author.yml` (unreferenced, stale email and location).
- Theme development files: `package.json`, `package-lock.json`, `banner.js`,
  `minimal-mistakes-jekyll.gemspec`, `Rakefile`, `CHANGELOG.md`, `staticman.yml`,
  theme `README.md` (replaced by a short site README), `screenshot*.png`.
- Every other file under `_includes/`, `_layouts/`, `_sass/`, `assets/js/`.

### Config

`_config.yml` is rebuilt from the 4.28.1 template with the site's values copied in:
locale, title, name, description, url, repository, logo, masthead title, Disqus
shortname, search (lunr), Algolia keys, reCAPTCHA secret, author profile and links,
footer links, kramdown settings, defaults (single layout, author profile, read time,
comments, share, related), include `_pages`, category/tag archive paths, pagination.
Site-specific additions: `remote_theme` pinned, `jekyll-include-cache` in `plugins`,
`after_footer_scripts: ["/assets/js/add_line_number.js"]`, `slapform-ID` (used by the
contact page), `analytics` block prepared for `google-gtag`.

`Gemfile` becomes the GitHub Pages recipe: `github-pages` and `jekyll-include-cache`
in the `jekyll_plugins` group. `Gemfile.lock` is committed for reproducible local
builds (remove it from `.gitignore`).

## Builder calls (Claude's, flagged for the owner)

- **MathJax stays on version 2, loaded directly from cdnjs (2.7.9, same `TeX-MML-AM_CHTML`
  config).** The legacy `cdn.mathjax.org` URL now serves a shim that redirects to 2.7.1 on
  cdnjs, so this removes a hop without changing behavior. MathJax 3 was considered and
  rejected: the four math posts use kramdown `$$` blocks, which emit `script type="math/tex"`
  tags that MathJax 3 does not render without extra configuration.
- **Dead code is deleted rather than carried** (contact-form include, card include,
  cards stylesheet, author.yml). All of it stays in git history.
- **Local build uses the `github-pages` gem** so the preview matches what GitHub Pages
  will build (Jekyll 3.10 and its plugin set), rather than a newer Jekyll 4.
- **Rollback safety:** tag the current `master` as `pre-theme-upgrade` before merging.
- **Cards unchanged in style**: the owner's "work on post cards" is the archive list
  change (teaser + full excerpt + Read more). It is kept for lists and deliberately not
  applied to related posts or the course grid, matching the live site.

## Verification (what "done" means)

1. `bundle exec jekyll build` on the branch completes with no errors or Liquid warnings.
2. Every `<loc>` in the live `https://techytok.com/sitemap.xml` exists as a file in `_site/`.
3. Spot checks in the built HTML: MathJax script present; line-number script present;
   GA snippet absent until the ID is supplied; no `tinyletter` string anywhere; Disqus
   embed present on a lesson; sidebar nav present on a lesson; four related cards on a
   lesson; course page lists its articles; "Read more" present on the home list.
4. Owner previews with `bundle exec jekyll serve`, compares with the live site, and
   reports anything to adjust.
5. Only after the owner's go: merge `theme-upgrade` into `master` and push. GitHub
   Pages rebuilds from `master`.

## Yearly sync procedure (goes into README)

1. Check https://github.com/mmistakes/minimal-mistakes/releases for the new version.
2. In a branch, change the version in `remote_theme`, run `bundle update`, rebuild.
3. For each override file in the table above, diff upstream old-version vs new-version
   for that path and re-apply any upstream change on top of the override.
4. Preview, compare, merge.

## Left to the builder

- Exact wording of the new README and the updated CLAUDE.md.
- Order of operations inside the branch, as long as each step leaves a buildable tree
  once Ruby is available.
