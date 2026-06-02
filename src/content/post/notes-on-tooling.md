---
title: "Notes on the tooling"
publishDate: 2026-01-25
description: "What's underneath this template — Astro, Pagefind, KaTeX — and why each piece is here."
tags: [ tooling, internals, astro ]
---

A short tour of the moving parts so you know what to keep and what to rip out. Most of the integrations below are
optional, leave the config field undefined and the script never loads.

## Astro

This is an [Astro](https://astro.build) site, statically generated. Pages live in `src/pages/`, content in
`src/content/`. Astro's content layer (the `glob` loader) reads markdown into typed collections. Routes are file-based.

If you ever need a server-rendered page, switch the page's `export const prerender = false`. Most of this template is
fully static and will deploy as a folder of HTML on any host — Cloudflare Pages, Netlify, GitHub Pages, S3.

## Markdown extensions

| Plugin                                         | Why                                                                       |
|------------------------------------------------|---------------------------------------------------------------------------|
| `remark-math` + `rehype-katex`                 | Inline and display math via `$…$` and `$$…$$`                             |
| `remark-directive` + custom remark-admonitions | The `:::note` / `:::tip` / `:::caution` containers                        |
| `astro-expressive-code`                        | Code blocks with syntax themes, copy button, terminal frames              |
| `rehype-external-links`                        | External `<a>` tags get `target="_blank"` and `rel="nofollow noreferrer"` |
| `rehype-unwrap-images`                         | Images at paragraph-root level escape the wrapping `<p>`                  |

## Search

Pagefind builds a full-text index at `dist/pagefind/` after every `pnpm build`. The template doesn't ship a search UI —
wire one up with `pagefind-default-ui` if you want.

## Analytics

Both [GA4](https://analytics.google.com) and [Goatcounter](https://www.goatcounter.com) are pre-wired and load
via [Partytown](https://partytown.qwik.dev/) so they run on a worker thread, not the main thread. Set the IDs in
`siteConfig.analytics`. Leave them undefined to skip the scripts.
