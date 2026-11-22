+++
title = 'Techly 2.5.1: Turning Off the Table of Contents'
date = 2026-11-22T10:00:00+01:00
year = '2026'
draft = false
description = 'Techly 2.5.1 respects toc = false on posts and no longer shows an empty table of contents box. This post has headings, but no table of contents.'
tags = ['feature']
series = ['Using Techly']
toc = false
index = true
+++

[Techly 2.5.1](https://github.com/m1rm/techly/releases/tag/v2.5.1) is a small bug fix release. **This post is the demo:** it has several headings, but sets `toc = false`, so there is no table of contents on the right.

## TL;DR

- `toc = false` in front matter now hides the table of contents on posts.
- Posts and pages without matching headings no longer show an empty "Contents" box.
- Defaults are unchanged: posts show a table of contents, pages only with `toc = true`.

## What was wrong

Two things, reported in [issue #3](https://github.com/m1rm/techly/issues/3):

1. **`toc = false` was ignored on posts.** The post layout rendered the table of contents without looking at the front matter.
2. **Empty boxes.** Posts without headings still got a gray "Contents" box with nothing in it.

The second one is a Hugo detail worth knowing. `.TableOfContents` is never an empty string. Without headings, Hugo returns an empty wrapper:

```html
<nav id="TableOfContents"></nav>
```

And if a post only has headings deeper than the configured range (say `####` with the default range of `##` to `###`), you get an empty skeleton:

```html
<nav id="TableOfContents">
  <ul>
    <li>
      <ul>
        <li></li>
      </ul>
    </li>
  </ul>
</nav>
```

Checking the length of that output, as Techly did, always says "there is something here".

## How it works now

Techly renders the table of contents only when it contains at least one link, meaning at least one heading within your `markup.tableOfContents` range. Posts additionally check `toc`:

| Front matter | Headings in range | Table of contents |
| --- | --- | --- |
| *(not set)* | yes | shown |
| *(not set)* | no | hidden |
| `toc = false` | yes | hidden, like on this post |
| `toc = true` | no | hidden |

Pages work the same way, except that they need `toc = true` to show one at all.

The article width stays the same with or without a table of contents, so line lengths don't jump between posts.

## Turning it off

Set `toc` in the post's front matter:

```toml
+++
title = 'A Short Note'
toc = false
+++
```

To compare, open any other post on this site, for example [Controlling Search Indexing]({{< relref "controlling-search-indexing" >}}). It has `toc = true` and shows the "Contents" box next to the article.

## Which headings count

The range comes from your site configuration. Hugo's default is `##` and `###`:

```toml
[markup.tableOfContents]
  startLevel = 2
  endLevel = 3
```

Headings outside that range don't produce table of contents entries, and with this release they no longer produce an empty box either.

## Upgrading

```bash
hugo mod get github.com/m1rm/techly/v2@v2.5.1
```

Update `version` in your module import in `hugo.toml` to `v2.5.1`. There are no breaking changes. If you skipped 2.5.0, you also get [canonical links and the new 404 page]({{< relref "techly-2-5-0-canonical-links-and-404-page" >}}).
