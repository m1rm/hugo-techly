+++
title = 'Techly 2.6.0: Notices'
date = 2026-11-25T10:00:00+01:00
year = '2026'
draft = false
description = 'Techly 2.6.0 ships a notice shortcode for info, tip, warning and caution boxes. Colors, icons and titles are configurable, and you can add your own types.'
tags = ['feature']
series = ['Using Techly']
toc = true
index = true
pinned = true
pinnedIndicator = true
+++

[Techly 2.6.0](https://github.com/m1rm/techly/releases/tag/v2.6.0) adds notice boxes: short, highlighted passages for background information, tips and warnings. They look like the theme's blockquotes, with a colored border, an icon and a title.

{{< notice info >}}
This is a notice. The rest of this post shows every type, how to configure them, and how to add your own.
{{< /notice >}}

## TL;DR

- `{{</* notice info */>}}…{{</* /notice */>}}` renders a notice box. The content is Markdown.
- Four built-in types: `info`, `tip`, `warning` and `caution`.
- Titles, icons and colors are configured in `hugo.toml` or `hugo.yaml` under `params.notices`.
- Add your own types in the same place, like the violet "Keep in mind" box on this site.
- Most content written for hugo-notice keeps working.

## The four types

{{< notice info >}}
**`info`** is the default. Use it for background information that helps, but isn't essential.
{{< /notice >}}

{{< notice tip >}}
**`tip`** is for helpful advice: a shortcut, a better way, a setting worth knowing.
{{< /notice >}}

{{< notice warning >}}
**`warning`** is for something to watch out for, such as a surprising default or a limitation.
{{< /notice >}}

{{< notice caution >}}
**`caution`** is for actions that can break something or lose data. Use it sparingly, so it keeps its weight.
{{< /notice >}}

Each box picks its colors from your system's light or dark mode. Switch your system's color scheme to see the other variant. The default colors meet WCAG AA contrast (at least 4.5:1) against the box background in both modes.

## Writing a notice

Put the type after the shortcode name and the content between the opening and closing tag:

```markdown
{{</* notice tip */>}}
Run `hugo server --navigateToChanged` to jump to the page you are editing.
{{</* /notice */>}}
```

The content is Markdown, so links, **bold** text, `code` and lists all work:

{{< notice tip >}}
Run `hugo server --navigateToChanged` to jump to the page you are editing. It works well together with:

- `--disableFastRender` for a full rebuild on every change
- `--buildDrafts` to see unpublished posts
{{< /notice >}}

Without a type, a notice is `info`:

```markdown
{{</* notice */>}}
Same as notice info.
{{</* /notice */>}}
```

### Custom titles

Each type has a default title. Pass a different one as the second argument:

```markdown
{{</* notice warning "Breaking change" */>}}
The cover image parameter is now `featuredImage`.
{{</* /notice */>}}
```

{{< notice warning "Breaking change" >}}
The cover image parameter is now `featuredImage`.
{{< /notice >}}

Named parameters work too, if you prefer them:

```markdown
{{</* notice type="tip" title="Shortcut" */>}}
…
{{</* /notice */>}}
```

## Configuring notices

The built-in types are defined in the theme's own `hugo.toml`:

```toml
[params.notices.info]
  title = "Info"
  icon = "notice-info"
  color = { light = "#0969da", dark = "#4493f8" }
```

Every type has three keys:

| Key | Description |
| --- | --- |
| `title` | Default title |
| `icon` | Name of an SVG in `assets/icons/` (without `.svg`), or `""` for no icon |
| `color` | Border, title and icon color: one value for both modes, or `{ light = "…", dark = "…" }` |

Hugo merges the theme's settings with your site's, key by key. You only write down what you want to change. Everything else keeps its default.

### Colors

One value applies in light and dark mode:

```toml
[params.notices.info]
  color = "#005f73"
```

To change only one mode, set just that key. The other one keeps the theme's default:

```toml
[params.notices.warning.color]
  dark = "#e3b341"
```

{{< notice caution >}}
Check the contrast of your own colors. The title text uses the notice color on the box background, which is `#f6f8fa` in light mode and `#161b22` in dark mode. Aim for a contrast ratio of at least 4.5:1.
{{< /notice >}}

### Icons

`icon` is the name of an SVG file in your site's `assets/icons/` folder, without the `.svg` extension. Techly ships `notice-info`, `notice-tip`, `notice-warning` and `notice-caution`. Your own icons go next to them:

```toml
# uses assets/icons/rocket.svg
[params.notices.tip]
  icon = "rocket"
```

Techly puts the SVG directly into the page, so it can take the notice color. For that, your icon needs `stroke="currentColor"` (outline icons) or `fill="currentColor"` (filled icons) instead of a fixed color. Icons from [Tabler Icons](https://tabler.io/icons) work out of the box and match the built-in ones.

To hide the icon of a type, set it to an empty string:

```toml
[params.notices.caution]
  icon = ""
```

If Techly can't find an icon, the notice is still rendered without it, and `hugo` prints a warning with the path it looked for.

### Titles

`title` changes the default title of a type:

```toml
[params.notices.info]
  title = "Good to know"
```

On a multilingual site, use the i18n keys `notice_<type>` instead. Hyphens in the type name become underscores, so `keep-in-mind` is `notice_keep_in_mind`. For example, in `i18n/de.toml`:

```toml
notice_info = "Info"
notice_tip = "Tipp"
notice_warning = "Warnung"
notice_caution = "Vorsicht"
```

A title passed in the shortcode always wins. After that come the i18n key, `title` from the config, and finally the type name itself.

## Adding your own types

A new entry under `params.notices` creates a new type. This site adds a violet "Keep in mind" box in its `hugo.toml`, with a bookmark icon from `assets/icons/bookmark.svg`:

```toml
[params.notices.keep-in-mind]
  title = 'Keep in mind'
  icon = 'bookmark'
  color = { light = '#8250df', dark = '#ab7df8' }
```

```markdown
{{</* notice keep-in-mind */>}}
Custom types work exactly like the built-in ones.
{{</* /notice */>}}
```

{{< notice keep-in-mind >}}
Custom types work exactly like the built-in ones: custom titles, Markdown content, light and dark colors.
{{< /notice >}}

Type names become part of a CSS class (`notice--keep-in-mind`), so stick to lowercase letters, digits and hyphens.

{{< notice warning >}}
You can change the built-in types, but not remove them. Hugo always merges the theme's settings into your site's. An unused type costs two lines of CSS and nothing else.
{{< /notice >}}

### YAML

The same configuration in `hugo.yaml`, or in `config/_default/params.yaml` without the top-level `params:` key:

```yaml
params:
  notices:
    info:
      color: "#005f73"
    warning:
      color:
        dark: "#e3b341"
    keep-in-mind:
      title: Keep in mind
      icon: bookmark
      color:
        light: "#8250df"
        dark: "#ab7df8"
```

### Typos

A notice with an unknown type stops the build, so a typo doesn't go unnoticed:

```text
ERROR notice: unknown type "warnign" at "/blog/content/posts/my-post.md:12:1".
Configured types: caution, info, keep-in-mind, tip, warning.
```

## Styling with CSS

Each notice is an `<aside>` with a class for its type:

```html
<aside class="notice notice--tip" role="note">
  <p class="notice-title">
    <span class="notice-icon" aria-hidden="true"><svg>…</svg></span>
    <span>Tip</span>
  </p>
  <div class="notice-content"><p>…</p></div>
</aside>
```

The colors from your config end up in the CSS variable `--notice-color` on `.notice--<type>`. Techly loads them before your [`assets/css/custom.css`](https://github.com/m1rm/techly#custom-css), so anything else can be changed there, for example rounded corners for all notices:

```css
.notice {
  border-radius: 0 8px 8px 0;
}
```

## Coming from hugo-notice

Techly's shortcode is also called `notice`, and it knows hugo-notice's `info`, `tip` and `warning` types. Content written for [hugo-notice](https://github.com/martignoni/hugo-notice) keeps working, except for `note`. Either change those notices to `info`, or add a `note` type to your config:

```toml
[params.notices.note]
  title = "Note"
  icon = "notice-info"
  color = { light = "#0969da", dark = "#4493f8" }
```

Then remove hugo-notice from your module imports, because a module listed before Techly provides its own `notice` shortcode and takes precedence:

```toml
[module]
  # remove this import
  # [[module.imports]]
  #   path = 'github.com/martignoni/hugo-notice'
  [[module.imports]]
    path = 'github.com/m1rm/techly/v2'
    version = 'v2.6.0'
```

Then run `hugo mod tidy`. Your notices will use Techly's look from then on.

## Upgrading

```bash
hugo mod get github.com/m1rm/techly/v2@v2.6.0
```

Update `version` in your module import in `hugo.toml` to `v2.6.0`. There are no breaking changes. For the full list of changes, see the [changelog]({{< relref "changelog" >}}).
