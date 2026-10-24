# Theme Upgrade to Pinned Remote Minimal Mistakes 4.28.1 — Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Rebuild techytok.com on Minimal Mistakes 4.28.1 fetched at build time, keeping only content plus a short list of override files, so a yearly sync is a version bump.

**Architecture:** Jekyll's `remote_theme` (jekyll-remote-theme, bundled in the `github-pages` gem) downloads the theme at build time; any file present in the repo at the same path overrides the theme's copy. The work is: replace the Gemfile and config, delete every vendored theme file, write the handful of overrides from the 4.28.1 originals plus the owner's edits, verify the build against the live sitemap, and hand off for visual preview before merging to `master`.

**Tech Stack:** Jekyll 3.10.0 via `github-pages` gem 232 (what GitHub Pages runs), jekyll-remote-theme 0.4.3, kramdown 2.4, Ruby 3.x installed by the owner.

**Spec:** `docs/superpowers/specs/2026-10-24-theme-upgrade-design.md`

## Global Constraints

- Work only on branch `theme-upgrade` (already exists, holds the spec). Never commit to `master` until the owner has previewed and approved.
- Theme version is exactly `mmistakes/minimal-mistakes@4.28.1`.
- Upstream files are taken from the fetched tag with `git show 4.28.1:<path>`; the tag is already in this repo's refs. Never hand-type an upstream file.
- Keep every file under `_posts/`, `_pages/`, `_drafts/`, `assets/images/`, `assets/colab/`, `assets/favicon/`, plus `index.html`, `CNAME`, `assets/cv_and_publications.pdf`, `_data/navigation.yml`, `_data/from_zero_to_julia.json`.
- No TinyLetter, no Universal Analytics (`UA-`) string anywhere in the repo or build output.
- Analytics provider stays `false` until the owner supplies a `G-` measurement ID.
- Sandbox: `bundle install` needs hosts `rubygems.org` and `index.rubygems.org`; `jekyll build` needs `github.com` and `codeload.github.com` for the theme download.
- Commit after every task with the Co-Authored-By trailer from the session reminder.

## Review Focus

Spec-implied cases no single task's check exercises; each has a test added to the owning task.

1. A post without `header.teaser` on the home list must render no `<img>` and no empty teaser `<div>` (Task 4).
2. An excerpt containing Markdown links must render as plain text before "Read more" (Task 4, `strip_html`).
3. A page without `acknowledgements` must get no empty "Acknowledgements" heading (Task 5).
4. The course landing page grid must show one card per entry of `from_zero_to_julia.json` (Task 7).
5. The lesson sidebar nav (`sidebar: nav: "zero-to-julia"`) must still render after `nav_list` comes from the remote theme (Task 7).

---

## Before starting (owner)

Install Ruby and Jekyll per https://jekyllrb.com/docs/installation/ubuntu/. Then confirm:

```bash
ruby -v      # 3.x
gem -v
bundle -v    # if missing: gem install bundler
```

---

### Task 1: Safety tag, Gemfile for GitHub Pages, toolchain check

**Files:**
- Modify: `Gemfile` (replace contents), `.gitignore` (remove the `Gemfile.lock` line)
- Create: `Gemfile.lock` (generated)

**Interfaces:**
- Produces: a working `bundle exec jekyll` at version 3.10.0 that later tasks build with; git tag `pre-theme-upgrade` on current `master` for rollback.

- [ ] **Step 1: Tag the current master for rollback**

Run: `git tag pre-theme-upgrade master && git tag -l pre-theme-upgrade`
Expected: prints `pre-theme-upgrade`

- [ ] **Step 2: Replace `Gemfile`**

```ruby
source "https://rubygems.org"

gem "github-pages", group: :jekyll_plugins
gem "jekyll-include-cache", group: :jekyll_plugins
gem "webrick" # needed by `jekyll serve` on Ruby 3
```

- [ ] **Step 3: Stop ignoring `Gemfile.lock`**

Delete the line `Gemfile.lock` from `.gitignore`.

- [ ] **Step 4: Install gems**

Run: `bundle install`
Expected: ends with `Bundle complete!`; `Gemfile.lock` exists.

- [ ] **Step 5: Verify Jekyll version matches GitHub Pages**

Run: `bundle exec jekyll -v`
Expected: `jekyll 3.10.0`

- [ ] **Step 6: Commit**

```bash
git add Gemfile Gemfile.lock .gitignore
git commit -m "Switch Gemfile to github-pages gem for Pages-identical local builds"
```

---

### Task 2: Rebuild `_config.yml` from the 4.28.1 template

**Files:**
- Modify: `_config.yml` (replaced wholesale)

**Interfaces:**
- Produces: `site.slapform-ID` (used by `_pages/contact-me.md`), `site.after_footer_scripts` (loads the line-number script), `remote_theme` pinned.

- [ ] **Step 1: Start from the upstream template**

Run: `git show 4.28.1:_config.yml > _config.yml`

- [ ] **Step 2: Set the site values**

Edit these keys in place (keep upstream comments and layout). Values are verbatim from the pre-upgrade config:

| Key | Value |
|---|---|
| `remote_theme` | `"mmistakes/minimal-mistakes@4.28.1"` (insert the line directly above `minimal_mistakes_skin`) |
| `locale` | `"en-GB"` |
| `title` | `"TechyTok"` |
| `name` | `"Aurelio Amerio"` |
| `description` | `"A logbook for my little projects and ideas"` |
| `url` | `"https://techytok.com"` |
| `repository` | `aurelio-amerio/aurelio-amerio.github.io` |
| `logo` | `"/assets/images/logo.png"` |
| `masthead_title` | `"TechyTok!"` |
| `comments.provider` | `"disqus"` |
| `comments.disqus.shortname` | `"techytok"` |
| `reCaptcha.siteKey` / `reCaptcha.secret` | copy both values from `git show master:_config.yml` |
| `search` | `true` |
| `search_provider` | `lunr` |
| `algolia.application_id`, `algolia.index_name`, `algolia.search_only_api_key` | copy from `git show master:_config.yml` |
| `analytics.provider` | `false` (leave `google.tracking_id` empty; comment above it: `# GA4: set provider to "google-gtag" and tracking_id to the G- measurement ID`) |
| `author` block | name, avatar, bio, location, and the six `links` entries exactly as in `git show master:_config.yml` lines 112-133 |
| `footer.links` | the six entries exactly as in `git show master:_config.yml` lines 134-152 |
| `defaults` posts scope | `comments: true` (template has it commented out); keep `layout: single`, `author_profile: true`, `read_time: true`, `share: true`, `related: true` |
| `defaults` pages scope | add a second entry: `path: ""`, `type: pages`, values `layout: single`, `author_profile: true` |
| `paginate` | `5` |

- [ ] **Step 3: Add the site-specific keys**

Directly below `masthead_title`:

```yaml
slapform-ID              : "iYIdvx4cQ" # used by _pages/contact-me.md
after_footer_scripts:
  - /assets/js/add_line_number.js
```

- [ ] **Step 4: Validate the YAML and the values**

Run:
```bash
ruby -ryaml -e 'c=YAML.load_file("_config.yml"); puts c["remote_theme"], c["after_footer_scripts"].inspect, c["defaults"].size, c["comments"]["provider"], c["analytics"]["provider"].inspect'
```
Expected:
```
mmistakes/minimal-mistakes@4.28.1
["/assets/js/add_line_number.js"]
2
disqus
false
```

- [ ] **Step 5: Confirm no dead-service strings remain in config**

Run: `grep -n 'tinyletter\|UA-\|line_number\|slapform-mail' _config.yml`
Expected: no output.

- [ ] **Step 6: Build with vendored files still present (sanity check of the toolchain)**

Run: `bundle exec jekyll build 2>&1 | tail -3`
Expected: last line contains `done in`. (The vendored 4.17.2 files still override the remote theme at this point; that is expected.)

- [ ] **Step 7: Commit**

```bash
git add _config.yml
git commit -m "Rebuild _config.yml from Minimal Mistakes 4.28.1 template, pin remote theme"
```

---

### Task 3: Delete the vendored theme and dead code; build on stock 4.28.1

**Files:**
- Delete: everything listed in Step 1
- Replace with upstream copies: `_includes/archive-single.html`, `_layouts/single.html`, `assets/css/main.scss`, `_data/ui-text.yml`
- Create: `_includes/archive-single-plain.html`, `_includes/page__related.html` (upstream copies, customized in Task 4)

**Interfaces:**
- Produces: a repo that builds on pure 4.28.1 with zero visual customizations. Task 4 onward layer the owner's edits on these exact files.

- [ ] **Step 1: Remove vendored theme files, keeping only the override set**

```bash
git ls-files _includes _layouts _sass assets/js assets/css \
  | grep -vE '^(_includes/(head/custom\.html|footer/custom\.html|archive-single\.html|archive-single-plain\.html|post_collection\.html)|_layouts/single\.html|_sass/_add_line_numbers\.scss|assets/js/add_line_number\.js|assets/css/main\.scss)$' \
  | xargs git rm -q
git rm -q package.json package-lock.json banner.js minimal-mistakes-jekyll.gemspec Rakefile CHANGELOG.md staticman.yml _data/author.yml
```

- [ ] **Step 2: Verify the surviving override set**

Run: `git ls-files _includes _layouts _sass assets/js assets/css _data | sort`
Expected exactly:
```
_data/from_zero_to_julia.json
_data/navigation.yml
_data/ui-text.yml
_includes/archive-single-plain.html
_includes/archive-single.html
_includes/footer/custom.html
_includes/head/custom.html
_includes/post_collection.html
_layouts/single.html
_sass/_add_line_numbers.scss
assets/css/main.scss
assets/js/add_line_number.js
```
Also: `git ls-files | grep -cE '^(package|banner|Rakefile|CHANGELOG|staticman|minimal-mistakes)'` prints `0`.

- [ ] **Step 3: Overwrite the theme-derived overrides with their 4.28.1 originals**

```bash
git show 4.28.1:_includes/archive-single.html > _includes/archive-single.html
git show 4.28.1:_includes/archive-single.html > _includes/archive-single-plain.html
git show 4.28.1:_includes/page__related.html > _includes/page__related.html
git show 4.28.1:_layouts/single.html        > _layouts/single.html
git show 4.28.1:assets/css/main.scss        > assets/css/main.scss
git show 4.28.1:_data/ui-text.yml           > _data/ui-text.yml
```

- [ ] **Step 4: Point the course grid at the plain card (unchanged name, confirm it)**

Run: `grep -n 'include' _includes/post_collection.html`
Expected: one line containing `archive-single-plain.html type="grid"`.

- [ ] **Step 5: Build on the stock theme**

Run: `bundle exec jekyll build 2>&1 | grep -iE 'error|warning|done in'`
Expected: a single `done in` line, no error or Liquid warning lines.

- [ ] **Step 6: Confirm the remote theme supplied the missing pieces**

Run: `grep -c 'class="masthead"' _site/index.html; grep -c 'nav__list' _site/lesson-functions/index.html; ls _site/assets/js/main.min.js _site/assets/css/main.css`
Expected: `1`, a number ≥ 1, and both file paths listed.

- [ ] **Step 7: Commit**

```bash
git add -A
git commit -m "Remove vendored Minimal Mistakes files; build on remote theme 4.28.1"
```

---

### Task 4: Re-apply the post-list card customization and the plain related-posts grid

**Files:**
- Modify: `_includes/archive-single.html`, `_includes/page__related.html`, `assets/css/main.scss`

**Interfaces:**
- Consumes: `_includes/archive-single-plain.html` (stock card, Task 3).
- Produces: list-mode cards with linked teaser image, full excerpt and "Read More"; related posts rendered with the plain card, four at most.

- [ ] **Step 1: Customize `_includes/archive-single.html` for list mode**

Starting from the 4.28.1 file, make exactly these changes:

1. Immediately after the `{% if include.type == "grid" and teaser %} ... {% endif %}` block, add a list-mode teaser block:
   ```liquid
   {% if include.type != "grid" and post.header.teaser %}
     <div class="archive__item-teaser">
       <a href="{{ post.url | relative_url }}"><img src="{{ teaser | relative_url }}" alt="{{ post.title }}" width="575" height="216"></a>
     </div>
   {% endif %}
   ```
2. Replace the excerpt line with:
   ```liquid
   {% if post.excerpt %}<p class="archive__item-excerpt p-summary" itemprop="description">{{ post.excerpt | markdownify | strip_html }}[&nbsp;<a href="{{ post.url | relative_url }}">Read&nbsp;More...</a>&nbsp;]</p>{% endif %}
   ```
Keep the upstream `<h2>` title and the `page__meta.html` include. (Builder call: the live site used `<h3>`; the class drives the styling, so `<h2>` is kept to stay closer to upstream. Swap to `<h3>` if the owner sees a size difference.)

- [ ] **Step 2: Make related posts use the plain card**

In `_includes/page__related.html`, change `{% include archive-single.html locale=locale type="grid" %}` to `{% include archive-single-plain.html locale=locale type="grid" %}`. The upstream `limit:4` already caps the grid at four.

- [ ] **Step 3: Add the line-number stylesheet import**

Append to `assets/css/main.scss` after the `@import "minimal-mistakes";` line:
```scss
@import "add_line_numbers"; // numbered code blocks (site override)
```

- [ ] **Step 4: Build and check the home list**

Run:
```bash
bundle exec jekyll build 2>&1 | grep -iE 'error|warning|done in'
grep -c 'Read&nbsp;More' _site/index.html
grep -c 'width="575" height="216"' _site/index.html
```
Expected: `done in` only; both counts equal `5` (one per paginated post).

- [ ] **Step 5: Review-focus check: a post without a teaser gets no image block**

Run:
```bash
f=$(grep -L 'teaser' _posts/*.md | head -1); echo "$f"
```
If a file prints, take its `permalink` and run `grep -c 'archive__item-teaser' _site/<permalink>/index.html` on a list page that shows it (the page of `_site/page*/index.html` containing its title). Expected: the count on that page equals the number of listed posts that do have a teaser. If no file prints, record "all posts have teasers" and move on.

- [ ] **Step 6: Review-focus check: Markdown in an excerpt is stripped**

Run: `grep -o 'archive__item-excerpt[^<]*' _site/index.html | grep -c '\]('`
Expected: `0` (no raw Markdown link syntax survives in any excerpt; the only bracket allowed is the literal `[&nbsp;` before "Read More", which the pattern above does not match).

- [ ] **Step 7: Check related posts on a lesson**

Run: `grep -c 'grid__item' _site/lesson-functions/index.html; grep -c 'Read&nbsp;More' _site/lesson-functions/index.html`
Expected: `4` and `0`.

- [ ] **Step 8: Check line numbers**

Run: `grep -c 'add_line_number.js' _site/lesson-functions/index.html; grep -c 'line-number' _site/assets/css/main.css`
Expected: `1` and a number ≥ 1.

- [ ] **Step 9: Commit**

```bash
git add _includes/archive-single.html _includes/page__related.html assets/css/main.scss
git commit -m "Re-apply post list cards, plain related posts, and line numbers on 4.28.1"
```

---

### Task 5: Re-apply the "Acknowledgements" section in the post layout

**Files:**
- Modify: `_layouts/single.html`

- [ ] **Step 1: Insert the section**

In the 4.28.1 `_layouts/single.html`, directly after the `</section>` that closes `<section class="page__content e-content" itemprop="text">` (upstream line 54) and before `<footer class="page__meta">`, insert:

```liquid
      {% if page.acknowledgements %}
      <section class="page__content" itemprop="text">
        <h1>Acknowledgements</h1>
        {{ page.acknowledgements | markdownify }}
      </section>
      {% endif %}
```

- [ ] **Step 2: Build and check the course page and a plain lesson**

Run:
```bash
bundle exec jekyll build 2>&1 | grep -iE 'error|warning|done in'
grep -c '<h1>Acknowledgements</h1>' _site/from-zero-to-julia/index.html
grep -c 'Acknowledgements' _site/lesson-functions/index.html
```
Expected: `done in`; `1`; `0` (review-focus item 3).

- [ ] **Step 3: Commit**

```bash
git add _layouts/single.html
git commit -m "Re-apply acknowledgements section to single layout"
```

---

### Task 6: Head snippets, footer links, navigation

**Files:**
- Modify: `_includes/head/custom.html`, `_data/navigation.yml`
- Keep as is: `_includes/footer/custom.html`

- [ ] **Step 1: Rewrite `_includes/head/custom.html`**

Keep the upstream wrapper comments. Contents between them: the MathJax script with `src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.9/MathJax.js?config=TeX-MML-AM_CHTML"` (same attributes as the old tag otherwise), then the nine favicon/manifest/meta lines unchanged from the current file. No analytics snippet.

- [ ] **Step 2: Hard-code the Slapform ID in the nav link**

In `_data/navigation.yml`, replace `https://slapform.com/form?id={slapform-ID}` with `https://slapform.com/form?id=iYIdvx4cQ`.

- [ ] **Step 3: Build and check**

Run:
```bash
bundle exec jekyll build 2>&1 | grep -iE 'error|warning|done in'
grep -c 'cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.9' _site/index.html
grep -rl 'UA-40944210\|tinyletter\|cdn.mathjax.org\|{slapform-ID}' _site | wc -l
grep -c 'slapform.com/form?id=iYIdvx4cQ' _site/index.html _site/contact-me/index.html
grep -c 'Terms &amp; policies' _site/index.html
```
Expected: `done in`; `1`; `0`; both files report ≥ 1; `1`.

- [ ] **Step 4: Commit**

```bash
git add _includes/head/custom.html _data/navigation.yml
git commit -m "Head: MathJax 2.7.9 from cdnjs, drop UA analytics; nav: inline Slapform ID"
```

---

### Task 7: Whole-site verification against the live sitemap

**Files:**
- None modified. Produces a verification record in the task output.

- [ ] **Step 1: Clean build**

Run: `rm -rf _site && bundle exec jekyll build 2>&1 | grep -iE 'error|warning|done in'`
Expected: `done in` only.

- [ ] **Step 2: Every live URL exists in the build**

Run:
```bash
curl -s https://techytok.com/sitemap.xml | grep -oP '(?<=<loc>)[^<]+' | sed 's#https://techytok.com##' \
 | while read -r p; do f="_site$p"; [ -f "$f" ] || [ -f "${f%/}/index.html" ] || echo "MISSING $p"; done
```
Expected: no output (47 URLs on the live sitemap at planning time).

- [ ] **Step 3: Feature spot checks**

Run each; expected values follow:
```bash
grep -c 'disqus' _site/lesson-functions/index.html            # ≥ 1 (comments)
grep -c 'nav__sub-title\|nav__items' _site/lesson-functions/index.html   # ≥ 1 (course sidebar; review-focus 5)
grep -o '<div class="grid__item">' _site/from-zero-to-julia/index.html | wc -l  # equals the number of "title" entries in _data/from_zero_to_julia.json (review-focus 4)
python3 -I -c 'import json;print(len(json.load(open("_data/from_zero_to_julia.json"))["articles"]))'
ls _site/assets/cv_and_publications.pdf _site/assets/colab | head -3   # present
m=$(grep -l '\$\$' _posts/*.md | head -1); u=$(grep -m1 '^permalink:' "$m" | awk '{print $2}'); grep -c 'math/tex' "_site${u}index.html"   # ≥ 1 (kramdown emitted math for MathJax)
```

- [ ] **Step 4: Record results**

Paste the outputs of Steps 1-3 in the task report. Any `MISSING` line or mismatch is a blocker; fix the cause in the owning task's file and re-run.

---

### Task 8: README, CLAUDE.md, yearly sync notes

**Files:**
- Replace: `README.md`
- Modify: `CLAUDE.md`

- [ ] **Step 1: Write a site README**

Replace the theme README with a short site README covering: what the site is; local preview (`bundle install`, `bundle exec jekyll serve`, open http://127.0.0.1:4000); deployment (GitHub Pages from `master`); the override file table copied from the spec's "Kept as override files"; the "Yearly sync procedure" section copied from the spec, with the concrete diff command:
```bash
git fetch https://github.com/mmistakes/minimal-mistakes.git 'refs/tags/*:refs/tags/*'
git diff 4.28.1 <new-tag> -- _includes/archive-single.html _includes/page__related.html _layouts/single.html assets/css/main.scss _data/ui-text.yml _includes/head/custom.html _includes/footer/custom.html
```

- [ ] **Step 2: Update `CLAUDE.md`**

Rewrite the "What this is" and "Commands" sections: theme is now `remote_theme` pinned at 4.28.1 (not vendored); list the override files; drop the `npm run build:js` and `Rakefile` paragraphs; `bundle exec jekyll serve` unchanged. Keep the content-architecture and course sections as they are.

- [ ] **Step 3: Commit**

```bash
git add README.md CLAUDE.md
git commit -m "Document remote-theme setup and yearly sync procedure"
```

---

### Task 9: Preview handoff, then merge on the owner's go

- [ ] **Step 1: Serve the preview**

Run: `bundle exec jekyll serve --livereload`
Tell the owner: open http://127.0.0.1:4000 and compare with https://techytok.com (home list, a lesson with sidebar and related posts, the course page, contact page, a math post, a code-heavy lesson for line numbers).

- [ ] **Step 2: Apply any adjustments the owner requests**

Each adjustment goes into the override file that owns it (table in the spec), with a rebuild and the relevant Task 4-6 grep check, then a commit.

- [ ] **Step 3: On the owner's explicit go, merge and push**

```bash
git checkout master
git merge --no-ff theme-upgrade -m "Upgrade to Minimal Mistakes 4.28.1 as a pinned remote theme"
git push origin master pre-theme-upgrade
```
Then confirm the GitHub Pages build succeeds (repository Actions tab, "pages build and deployment") and https://techytok.com renders. Rollback if needed: `git reset --hard pre-theme-upgrade && git push --force-with-lease origin master` (owner's call only).

- [ ] **Step 4: Optional: GA4 ID**

When the owner supplies the `G-` ID: set `analytics.provider: "google-gtag"` and `analytics.google.tracking_id` in `_config.yml`, rebuild, check `grep -c 'googletagmanager.com/gtag/js?id=G-' _site/index.html` equals `1`, commit.
