+++
title = 'Techly 2.5.0: Canonical Links and a 404 Page'
date = 2026-11-22T09:00:00+01:00
year = '2026'
draft = false
description = 'Techly 2.5.0 adds a canonical link to every page, handles pagination correctly, and ships a 404 page that stays out of search results.'
tags = ['feature']
series = ['Using Techly']
toc = true
index = true
+++

[Techly 2.5.0](https://github.com/m1rm/techly/releases/tag/v2.5.0) is about search engines. It adds two things that Google Search Console tends to complain about when they are missing: canonical links and a proper 404 page.

## TL;DR

- Every page now has a `<link rel="canonical">` pointing to its own URL.
- Paginated lists (home, tags, series) point to the **current** page, so `/page/2/` is canonical to itself.
- A new `404.html` links back to the home and search pages and stays `noindex`.
- No configuration needed. Update the module version and rebuild.

## Why canonical links matter

The same page is often reachable under more than one URL: with and without `www`, over `http` and `https`, or with tracking parameters like `?utm_source=…`. Without a hint, search engines have to guess which URL is the real one. Search Console reports this as **"Duplicate without user-selected canonical"**.

A canonical link is that hint. Techly now adds one to the `<head>` of every page, built from your `baseURL`:

```html
<link rel="canonical" href="https://example.com/posts/my-article/">
```

> **Note:** a canonical link is a hint, not a redirect. If your site answers on both `www` and the bare domain, also redirect one to the other on your web server, and set `baseURL` to the host you want indexed.

## Pagination done right

Paginated pages are a classic pitfall. In Hugo, `.Permalink` on `/page/2/` returns the URL of page 1. Using it as the canonical would tell search engines that page 2 is a duplicate of page 1, and the posts listed only there would be harder to discover.

Techly uses the URL of the current pager instead:

| URL | Canonical |
| --- | --- |
| `/` | `/` |
| `/page/2/` | `/page/2/` |
| `/tags/feature/page/2/` | `/tags/feature/page/2/` |

This site is a good test case: it lives under a subpath (`/hugo-techly/`), and the canonical links include it correctly.

### Custom layouts

The `<head>` renders before the page content, so it cannot know which collection a list template paginates. Techly solves this with a `canonical` block in `baseof.html`. The home, term, and series templates override it.

If you override one of those templates with your own pagination, override the block too and paginate the same collection:

```go-html-template
{{ define "canonical" }}
  {{ partial "head/canonical.html" ((.Paginate .Pages).URL | absURL) }}
{{ end }}
```

Hugo builds a page's paginator only once, so both calls must use the identical collection.

## A real 404 page

Until now, missing URLs ended up on your web server's default error page. Techly 2.5.0 ships `layouts/404.html`, rendered with the regular site layout and links to the home and search pages.

It is built to stay out of search results:

- `<meta name="robots" content="noindex">`
- no canonical link
- not listed in `sitemap.xml`

GitHub Pages and Netlify serve `/404.html` automatically. On nginx, point `error_page` at it:

```nginx
error_page 404 /404.html;
```

Try it on this site: [a page that does not exist](/hugo-techly/this-page-does-not-exist/).

## Upgrading

Pin the new version in `hugo.toml`:

```toml
[module]
  [[module.imports]]
    path = 'github.com/m1rm/techly/v2'
    version = 'v2.5.0'
```

Then run:

```bash
hugo mod get github.com/m1rm/techly/v2@v2.5.0
```

There are no breaking changes. After deploying, use **Validate fix** in Search Console for the affected reports. For the full list of changes, see the [changelog](https://github.com/m1rm/techly/blob/main/CHANGELOG.md).
