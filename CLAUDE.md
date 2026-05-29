# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

Static landing site for the Vellum iOS app (vellumapp.net). No build step, no dependencies, no package manager — just hand-authored HTML/CSS/JS served as files. Deployed to Cloudflare Workers; the repo also retains GitHub Pages artifacts (`CNAME`, `.nojekyll`) from a prior deploy target.

## Local dev & deploy

Wrangler serves the repo root directly (`assets.directory = "."` in `wrangler.jsonc`).

- Preview locally: `npx wrangler dev`
- Deploy: `npx wrangler deploy`

There are no tests, no linter, and no build output — edits to `.html` are the deliverable.

## Site layout

- `index.html` — root gift-claim landing page (duplicate of `gift/index.html`, but with `/en/privacy` & `/en/terms` footer links instead of `/privacy` & `/terms`). Keep these two in sync when editing copy/markup.
- `gift/index.html` — universal-link target for `/gift` (see AASA below). Has its own inline TR/EN i18n switcher (independent from the per-locale directory pages).
- `<locale>/` (22 locales: `ar`, `de`, `el`, `en`, `es-es`, `es-mx`, `fr`, `he`, `hi`, `id`, `it`, `ja`, `ko`, `ms`, `pt-br`, `pt-pt`, `ro`, `ru`, `th`, `tr`, `vi`, `zh`) — each contains `index.html`, `privacy/index.html`, `support/index.html`, `terms/index.html`. These are fully self-contained pages, not templated — copy changes must be applied per-locale.
- RTL locales: `ar` and `he` use `<html dir="rtl">`. Preserve this when editing.
- `.well-known/apple-app-site-association` — Apple Universal Links config. The `_headers` file forces `Content-Type: application/json` on it (the file has no extension, so the header is required for iOS to accept it). The AASA registers `/gift` and `/gift/*` as app-link paths for bundle `GUEKT879D3.com.vellumapp.vellum` — moving or renaming `gift/` will break universal links.
- `_headers` — Cloudflare Pages-style header rules: AASA content-type override + site-wide security headers (`X-Content-Type-Options`, `Referrer-Policy`, `HSTS`).
- `CNAME` (`vellumapp.net`) and `.nojekyll` — leftovers from GitHub Pages hosting; harmless under Wrangler but don't remove without confirming hosting target.

## Editing conventions

- The root `index.html` and `gift/index.html` both ship inline i18n via a `data-i18n` attribute scheme and an `I18N` object in `<script>`. Adding a string requires updating the markup attribute *and* every language block in that script (currently TR + EN only in those two files).
- Per-locale directory pages (`<locale>/index.html` etc.) are NOT driven by that runtime i18n system — each is a separately-translated static file. A copy change in English must be replicated by hand into the other 21 locale files.
- `compatibility_date` in `wrangler.jsonc` is pinned; bump deliberately, not casually.
