# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

WarmApp Games' public website (https://warmappgames.github.io) — game pages, about, privacy policy / terms for app-store listings, and `app-ads.txt`. It is a content-customized fork of [daattali/beautiful-jekyll](https://github.com/daattali/beautiful-jekyll) (theme v6.0.1), served by GitHub Pages directly from `master`.

## Build, deploy, verify

There is **no working local build**: system Ruby is 2.6 and the Gemfile's dependency tree needs ≥ 3.0; no rbenv/brew Ruby is installed. GitHub Pages' own Jekyll build (legacy branch build from `master`) is the build pipeline — **every push to `master` deploys the live site within ~1 minute**.

Verification after a push:

```bash
gh run list --repo warmappgames/warmappgames.github.io --limit 3   # pages-build-deployment must succeed
curl -s -o /dev/null -w "%{http_code}\n" https://warmappgames.github.io/
```

The "github-pages gem can't satisfy your Gemfile" warning in the Pages build log is expected and harmless — Pages ignores the Gemfile and uses its pinned Jekyll. If Docker's daemon is running, a local smoke build is possible with a `ruby:3.3` container (`bundle install && bundle exec jekyll build`), matching the CI workflow's Ruby.

Before pushing, cheap local checks: `ruby -ryaml -e 'YAML.load_file("_config.yml")'` and eyeballing Liquid tag balance in changed layouts.

## Git specifics

- Pushing: the macOS keychain returns the `rus89` GitHub account, which has **no write access** (plain `git push` → 403). `rus89` is also gh's default active account (Milan uses it elsewhere). Switch to `warmappgames` for the push, then switch back:
  ```bash
  gh auth switch --user warmappgames
  git -c credential.helper= -c credential.helper='!gh auth git-credential' push origin master
  gh auth switch --user rus89
  ```
- Milan pushes directly to `master` in this repo (no PR flow) — remember each push is a production deploy.
- If a PR is ever needed: `gh pr create` here defaults to targeting the parent repo (daattali/beautiful-jekyll). Always pass `--repo warmappgames/warmappgames.github.io`.
- The `upstream` remote points at daattali/beautiful-jekyll. Syncing = `git merge upstream/master`; expect conflicts only in `_config.yml` and any customized layout.

## Architecture: theme vs. content

The split that keeps upstream merges nearly conflict-free:

- **Theme internals** (`_layouts/`, `_includes/`, `assets/css/`, `assets/js/`, `feed.xml`, `tags.html`, `404.html`) are upstream's code — keep them pristine. The one exception is `_layouts/home.html`, below.
- **Site content** is everything WarmApp-specific: `_config.yml` values, `index.html`, `aboutme.md`, `content/pages/*.md` (privacy/terms), `app-ads.txt`, `assets/img/` (logos, covers, screenshots).

Key mechanisms that span files:

- **Home page**: `index.html` is only front matter (`layout: home` + `cover-img` carousel list) — it has no body. `_layouts/home.html` wraps the theme's entire blog-posts list in `{% comment %}…{% endcomment %}` so the home page renders just the carousel/navbar. Don't "restore" that block: the `_posts/` in this repo are theme demo leftovers and must not appear. HTML comments cannot be used for this — the theme's markup contains nested `<!-- -->` comments, and Liquid executes inside HTML comments anyway.
- **Config toggles**: `title-on-all-pages`, `post_search`, and `edit_page_button` are deliberately `false` to keep the site's pre-v6 appearance. `share-links-active` has no email option (never existed in the theme).
- **Root `.md` files are published as pages** by Jekyll. `JOURNAL.md` and `CLAUDE.md` are in `_config.yml`'s `exclude` list; any new non-site file at the root must be added there too.
- Page front-matter parameters (`cover-img`, `readtime`, `thumbnail-img`, …) are documented in the upstream `README.md` under "Supported parameters".

## Journal

`JOURNAL.md` at the root is the project journal (per Milan's global rules): read it at session start, append insights and decisions, commit entries. It is excluded from the published site.
