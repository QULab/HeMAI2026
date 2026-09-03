# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

The website for HeMAI 2026, a workshop on Multimodal Interaction with Generative AI Health
Applications, held with ICMI 2026 on October 9, 2026 in Naples. It is a Jekyll site using the
stock `minima` theme, served by GitHub Pages from the `docs/` folder on `main`.

Live at <https://qulab.github.io/HeMAI2026/>.

## Commands

Everything runs from `docs/`, which is the Jekyll root:

```bash
cd docs && bundle exec jekyll serve
```

```bash
cd docs && bundle exec jekyll build
```

There are no tests, linters, or a build step beyond Jekyll.

Always use `bundle exec`. The Gemfile declares `github-pages`, which pins Jekyll 3.10 and
minima 2.5 — the same versions GitHub Pages runs. A bare `jekyll` on the PATH is a newer 4.x
and will not reproduce the deployed output.

## Deploying

Pushing to `main` is the deploy. GitHub Pages rebuilds on its own, typically within a minute;
there is no Actions workflow. To confirm a change actually went out, poll the live URL rather
than trusting the push:

```bash
curl -s "https://qulab.github.io/HeMAI2026/assets/main.css?cb=$(date +%s)" | grep -c organizer-card
```

Note that `assets/main.css` is served with `cache-control: max-age=600`, so a browser will keep
showing the old stylesheet for up to ten minutes after a correct deploy. When a visual change
looks like it did not land, check the served file before changing any code.

## Things that will bite you

**The stylesheet must live at `docs/assets/main.scss`.** minima hardcodes
`/assets/main.css` in its `<head>`. A stylesheet at any other path compiles fine and is
reachable by URL, but is never linked, so every rule in it silently does nothing. This repo
lost a stylesheet that way once.

**Theme variables go before the `@import`.** `main.scss` sets `$content-width: 980px` above
`@import "{{ site.theme }}"`; minima declares its variables with `!default`, so this widens
every `.wrapper` the theme centres. Do not instead cap `.page-content` — minima centres one
`.wrapper` per section, and a max-width box around it has no auto margins, which pushes the
page content flush left while the header stays centred.

**Pages resolve to `.html`, not to directories.** There is no `permalink` setting, so link
with `{{ "/program.html" | relative_url }}`. A `/program/` style link 404s. Use `relative_url`
throughout rather than absolute `https://qulab.github.io/...` URLs, since `baseurl` is
`/HeMAI2026`.

**`docs/_includes/header.html` is a copy of minima's, edited.** It adds the banner image and
the site subtitle. Upstream theme changes will not reach it.

**Synology Drive syncs this directory.** Deleted files have come back on their own. After a
`git mv` or `rm`, re-check `git status` before assuming the removal stuck.

## Content

Five pages in `docs/`, all `layout: page`, listed in `header_pages` in `_config.yml` in nav
order. `index.md`, `program.md`, `call-for-papers.md`, `important-dates.md`, `organizers.md`.

The call for papers closed on July 13, 2026. `call-for-papers.md` is kept as an archive behind
a notice at the top and must not present itself as an open call — the live PCS submission link
was deliberately removed. Dates and status therefore need to stay consistent across
`index.md`, `important-dates.md` and `call-for-papers.md`; a change to one usually implies the
others.

`program.md` carries the schedule twice, as a linked PDF (`assets/hemai2026_program.pdf`) and
as a table. Both must be updated together. The table sits in a `<div class="program-table"
markdown="1">` — the attribute is what makes kramdown still parse the markdown table inside
raw HTML — and that wrapper gives it `overflow-x: auto`, so the table scrolls in its own box
on narrow screens instead of widening the page. The committee list on `organizers.md` uses
the same wrapper trick with `.committee-list`.

`organizers.md` holds the five organizer cards as raw HTML plus the 41-member program
committee list. Three committee members have no affiliation on purpose, pending their reply.

Images in `assets/` are served at a few hundred pixels; resize before committing. The banner
was once a 6 MB 4128px PNG displayed at 360px.
