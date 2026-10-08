# Vibetiles for HTML sites: add the design panel to this website

These are instructions for an AI assistant that builds or edits a website. Follow them in order. They add **Vibetiles**: a small design panel on the live page. The site's owner turns dials (colours, fonts, sizes, spacing, light and dark) and sees the page change. Visitors get a smaller panel (text size, light and dark, the styles the owner offers). The site keeps its own design until someone turns a dial.

![The Vibetiles panel, open on the Styles page](.github/panel.png)

The whole panel is one folder, `vibetiles/`. It needs no server, no build step and no account. Download it here and unzip it:

https://github.com/manuelesposito/vibetiles-html/releases/latest/download/vibetiles-html.zip

The zip holds the folder `vibetiles/` and these instructions as `vibetiles/README.md`.

## 1. Put the folder in the site

Copy the folder `vibetiles/` so that it is served from the site's root: `https://example.com/vibetiles/loader.js` must load.

- **Plain HTML**: next to `index.html`.
- **Vite, React, Next.js, Astro and similar**: inside the folder served as-is, usually `public/`.

Never edit the files inside `vibetiles/`. The one exception is `vibetiles/site.js`, and only in step 6.

## 2. One line in every page's head

Put this as the **first script** inside `<head>` on every page, before the page's own stylesheets and scripts:

```html
<script src="/vibetiles/loader.js"></script>
```

Rules:

- It must be a plain, blocking `<script>` tag. No `async`, no `defer`, no `type="module"`, and it must not be added from JavaScript after the page has loaded. The panel sets the page's look before the first paint; loaded late, the page would flash.
- In a framework, put it in the root layout's `<head>` as a plain tag, never through a script loader component.
- Use `/vibetiles/loader.js` from the site root. If the site lives in a subfolder, adjust the path.

## 3. Name the parts of the page

The panel finds the parts of a page by these class names. Add them to the elements that are already there. Do not add new elements, and do not restyle anything because of them.

| Part | Class to add |
| --- | --- |
| The page's main title (one per page) | `wp-block-post-title` |
| The main text of a page: an article, a service description, an about text | `wp-block-post-content` on the element that holds its paragraphs, headings, lists and quotes |
| Headings inside that text | `wp-block-heading` |
| A quote | `wp-block-quote` |
| The date of an article | `wp-block-post-date` |
| The author's name | `wp-block-post-author-name` |
| Categories or tags shown with an article | `wp-block-post-terms` |
| A short summary on a card or in a list | `wp-block-post-excerpt` |
| Picture captions | use `<figcaption>`, or add `wp-element-caption` |
| The site's name in the header | `wp-block-site-title` |
| The main navigation | `wp-block-navigation` on the `<nav>`; `wp-block-navigation-item__content` on each link |
| Buttons and button-like links | `wp-block-button__link` |

Only name what the page has. A page without an article simply has no `wp-block-post-content`.

The panel's button places itself in the navigation. Leave room for one more item there.

## 4. Colours: let the panel reach them

Once the owner or a visitor picks a style, the panel paints the page: background, text, links, light or dark. It can only reach colours the page does not fix itself. So look at every element that sets its **own background colour** in the site's CSS (sections, bands, cards, boxes, the header, the footer) and sort it into one of two kinds.

**It belongs to the page** (a white, cream or light grey section, a card, a box): under a chosen style it takes the panel's colours. Add a rule for it, next to its own rule in the site's CSS:

```css
:root[data-chosen] .services { background-color: var(--surface-canvas) !important; color: var(--text-primary) !important; }
:root[data-chosen] .card     { background-color: var(--surface-base) !important;   color: var(--text-primary) !important; }
```

| Use | For |
| --- | --- |
| `var(--surface-canvas)` | the page itself and full-width sections |
| `var(--surface-base)` | cards, boxes and panels that stand on the page |
| `var(--text-primary)` | text |
| `var(--text-secondary)` | quieter text: dates, notes, captions |
| `var(--accent)` | the style's accent colour |

If such an element also sets its own text colour, or its children do (headings, paragraphs, small print), give them the text rule too.

**It is part of the brand** (a strong colour on purpose: a dark hero, a coloured band, a logo block): it keeps its own background under every style. Then it must also keep its own text colour: make sure the element sets `color` itself, and that every heading, paragraph and link inside it sets its colour too, instead of inheriting the page's. Otherwise, under a dark style, light text lands on a light band, or dark text on a dark one.

`:root[data-chosen]` is only on the page once a style is picked. Until then these rules do nothing, and the site looks exactly as its own CSS says.

## 5. Check it

1. Open the site. It must look exactly as before, with one small extra button in the navigation.
2. Press the button. A visitor's panel opens: text size, light and dark, styles.
3. Open the site once with `?design` at the end of the address (for example `https://example.com/?design`). From now on this browser gets the owner's full panel. `?design=off` gives the visitor's panel back.
4. In the full panel, pick a style and turn a few dials. The page should change. Press Undo to go back.
5. Pick a style, switch to Dark, and scroll through every page. Every heading and every paragraph must be easy to read on what is behind it. Then do the same in Light.

If a part does not change when its dial moves, it is missing its class name from step 3. If text is hard to read under a style, its section is missing a rule from step 4.

## 6. Publishing

A website made of files cannot save itself. What the owner changes lives in their browser until it is published into `vibetiles/site.js`:

- **If you have the [Vibetiles Connector](https://github.com/manuelesposito/vibetiles-connector)**: when the owner asks you to publish, call its `publish` tool. The answer holds the new contents of `vibetiles/site.js`. Replace the whole file with it, then put the site online again, the same way you normally deploy this site.
- **Without the connector**: after the owner publishes in the panel, a bar reads "Not on your site yet" with a Copy button. The owner pastes that text to you. Replace the whole `vibetiles/site.js` with the file part of it, then deploy.

After the site is online again, the owner reloads the page. The bar goes away when the site carries the change.

Never write colours, fonts or sizes from the panel into the site's own CSS. The look lives in `vibetiles/site.js`, so the owner can keep changing it with dials.

## What not to do

- Do not copy the panel's CSS or scripts into the site's own files.
- Do not change how the site looks at rest. The rules from step 4 only act once a style is picked.
- Do not rename or move anything inside `vibetiles/`.
- Do not add the panel on only some pages: every page gets the one line from step 2.
- Do not describe the panel as an accessibility tool. It is a design tool.

## About

Vibetiles is a design panel built for AI, also a WordPress plugin: https://elmastudio.de/en/vibetiles/

GPL-2.0-or-later. By Elmastudio.
