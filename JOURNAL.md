# Journal

## 2026-08-07 — Sync fork with upstream beautiful-jekyll

- Fork had diverged from daattali/beautiful-jekyll at Dec 2020 (merge base 610af68): 24 ahead / 158 behind.
- Merged upstream/master (theme v6.0.1) into branch `sync-upstream`. Only 2 real conflicts:
  - `_config.yml`: took upstream's reorganized file, re-applied WarmApp Games values (title, author, navbar, Logo.png avatar, socials, rss-description, timezone Europe/Belgrade). Set new features `title-on-all-pages`, `post_search`, `edit_page_button` to false to keep the site visually unchanged — Milan can enable each with one line.
  - `_layouts/home.html`: the old hack hid the posts list by wrapping it in an HTML comment. Upstream's new markup contains nested `<!-- role="list" -->` comments which would break the outer comment (HTML comments don't nest) and make the posts list render. Replaced the wrapper with Liquid `{% comment %}...{% endcomment %}` — same intent (home shows only index.html content), correct mechanics.
- `share-links-active: email: true` (added 2021) was dropped: neither the old nor new theme's social-share.html ever supported an email option — it was always a no-op.
- `readtime: true` on privacy-policy/terms pages still supported in v6 (used by `_includes/header.html`).
- Local build verification impossible: system Ruby 2.6 too old (ffi needs ≥ 3.0), no rbenv/asdf/brew ruby, Docker daemon down. Verification plan: upstream's CI workflow (`.github/workflows/ci.yml`, Ruby 3.3) builds the site on every push.
- Push initially blocked: keychain + gh only had GitHub account `rus89` (no write access, 403). Milan added the warmappgames account to gh and skipped the PR: merged sync-upstream to master locally (fast-forward) and pushed directly. Push needs a one-off credential override because osxkeychain still returns rus89 first: `git -c credential.helper= -c credential.helper='!gh auth git-credential' push origin master` with warmappgames as gh's active account.
- Verified after push: Pages build succeeded, fork shows 0 behind / 26 ahead of upstream, live home page serves the new theme with no posts list, navbar/carousel intact, policy pages 200 (via normal trailing-slash 301).
