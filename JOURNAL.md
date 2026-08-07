# Journal

## 2026-08-07 — Sync fork with upstream beautiful-jekyll

- Fork had diverged from daattali/beautiful-jekyll at Dec 2020 (merge base 610af68): 24 ahead / 158 behind.
- Merged upstream/master (theme v6.0.1) into branch `sync-upstream`. Only 2 real conflicts:
  - `_config.yml`: took upstream's reorganized file, re-applied WarmApp Games values (title, author, navbar, Logo.png avatar, socials, rss-description, timezone Europe/Belgrade). Set new features `title-on-all-pages`, `post_search`, `edit_page_button` to false to keep the site visually unchanged — Milan can enable each with one line.
  - `_layouts/home.html`: the old hack hid the posts list by wrapping it in an HTML comment. Upstream's new markup contains nested `<!-- role="list" -->` comments which would break the outer comment (HTML comments don't nest) and make the posts list render. Replaced the wrapper with Liquid `{% comment %}...{% endcomment %}` — same intent (home shows only index.html content), correct mechanics.
- `share-links-active: email: true` (added 2021) was dropped: neither the old nor new theme's social-share.html ever supported an email option — it was always a no-op.
- `readtime: true` on privacy-policy/terms pages still supported in v6 (used by `_includes/header.html`).
- Local build verification impossible: system Ruby 2.6 too old (ffi needs ≥ 3.0), no rbenv/asdf/brew ruby, Docker daemon down. Verification plan: upstream's CI workflow (`.github/workflows/ci.yml`, Ruby 3.3) builds the site on every push.
- BLOCKED on push: keychain + gh only have GitHub account `rus89`, which has no write access to warmappgames/warmappgames.github.io (403). Milan's recent commits were likely made via GitHub web UI. Need Milan to either `gh auth login` as warmappgames or add rus89 as collaborator.
- PR must be opened with `--repo warmappgames/warmappgames.github.io` — `gh pr create` on a fork defaults to targeting the parent repo (daattali's).
