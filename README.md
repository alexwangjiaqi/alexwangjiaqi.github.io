# alexwangjiaqi.github.io

Personal site and blog built with **[Hugo](https://gohugo.io/)** (no third-party theme). Layout and typography take inspiration from [Gregory Gundersen’s site](https://gregorygundersen.com/): narrow measure, simple nav, understated links. The site splits **technical writing** (**Blog**) from lighter **Personal** notes, each with its own taxonomy and topic navigation.

Live configuration uses **Libre Franklin** by default (a classic, readable sans). Other presets are available via `hugo.toml` (see below).

---

## Requirements

- **Hugo** `0.120+` (tested on `0.160.x`). [Install Hugo](https://gohugo.io/installation/).

---

## Quick start

```bash
git clone https://github.com/alexwangjiaqi/alexwangjiaqi.github.io.git
cd alexwangjiaqi.github.io
hugo server
```

Open the URL Hugo prints (usually `http://localhost:1313/`).

**Production build** (writes to `public/`):

```bash
hugo --minify
```

Before deploying, set **`baseURL`** in `hugo.toml` to your real domain (or hosting URL).

---

## Repository layout

| Path | Purpose |
|------|--------|
| **`hugo.toml`** | Site title, `baseURL`, taxonomies, permalinks, math (KaTeX), Goldmark (`unsafe` HTML, math passthrough), nav tag lists, **font preset** (`params.site_font`). |
| **`content/`** | All Markdown pages and posts. |
| **`layouts/`** | HTML templates: base layout, section lists, tag pages, partials (head, nav, math, intros). **[Shortcodes](https://gohugo.io/content-management/shortcodes/)** in **`layouts/shortcodes/`** (e.g. **`theorem.html`**). |
| **`static/`** | Static assets copied verbatim; site CSS in `static/css/blog.css`; **global** images/PDFs in `static/img/` and `static/files/` (URLs `/img/...`, `/files/...`). |
| **`notebooks/`** | Jupyter sources for reproducibility (`<slug>.ipynb` matches `content/blog/<slug>/` or `content/personal/<slug>/`). Not built by Hugo. |
| **`data/site_fonts.yaml`** | Google Fonts URLs and family names for each `site_font` key. |
| **`archetypes/`** | Front-matter templates for `hugo new`. |
| **`.gitignore`** | Ignores `public/`, `resources/`, `.hugo_build.lock`, Jupyter checkpoints, common Python/venv paths. |

### `content/` in more detail (page bundles)

Posts are **[Hugo leaf bundles](https://gohugo.io/content-management/page-bundles/)**: one folder per slug, with `index.md` and any figures you want co-located.

```
content/
├── _index.md           # Home page front matter (home layout is custom)
├── about-me.md         # About page → /about-me/
├── blog/
│   ├── _index.md       # Blog section landing
│   └── my-slug/        # Post → /blog/YYYY/MM/DD/my-slug/
│       ├── index.md
│       ├── plot.png    # optional; reference as ![alt](plot.png)
│       └── diagram.svg
└── personal/
    ├── _index.md       # Personal section landing
    └── another-slug/
        ├── index.md
        └── photo.jpg
```

- **Blog** posts use taxonomy **`tags`** (URLs under `/tags/...`).
- **Personal** posts use **`personal_tags`** (URLs under `/personal/topic/.../`).

**Global assets** (site-wide, not per post): put files in **`static/img/`** or **`static/files/`** and link as **`/img/...`** or **`/files/...`**.

**Notebook / figures:** add **`notebooks/<slug>.ipynb`** (same slug as the post folder) to keep plotting code with the article. Optional front-matter field **`notebook_url`** can point at the GitHub blob URL for reference; the article body usually links to that notebook from **Sources** (there is no automatic link in the post header).

---

## Adding a Blog post

1. Create a **bundle** under **`content/blog/<slug>/`**:

   ```bash
   hugo new content blog/my-post-title/index.md
   ```

   Add figures in the same folder; reference them with a **relative** path in Markdown, e.g. `![](figure.png)`.

2. Edit front matter. Important fields:

   - **`title`**, **`date`**, **`draft`** (`false` when ready to publish).
   - **`subtitle`** (optional) — shown under the title.
   - **`tags`** — list of strings; should match labels you want under **Blog** topic navigation.

   Topic bars on list/tag pages are driven by **`params.tag_nav`** in `hugo.toml`. Use those exact strings (or update `hugo.toml` when you introduce new topics).

   Optional: **`notebook_url`** — full GitHub URL to **`notebooks/<slug>.ipynb`** (for your own reference; link it in the article if you want readers to open the notebook).

3. Write the body in **Markdown**. **Math**: KaTeX is enabled; use `$...$` / `$$...$$` (see `hugo.toml` `passthrough` delimiters).

Example (minimal):

```yaml
---
title: My note
date: 2026-04-10
draft: false
subtitle: Optional line under the title
tags:
  - Machine Learning
  - Misc
---

Your **markdown** here.
```

**Permalink pattern** (from `hugo.toml`): `/blog/:year/:month/:day/:slug/`.

---

## Adding a Personal post

1. Create a bundle under **`content/personal/<slug>/`**:

   ```bash
   hugo new content personal/my-note/index.md
   ```

2. Use **`personal_tags`** (not `tags`):

   ```yaml
   ---
   title: Saturday sketch
   date: 2026-04-10
   draft: false
   subtitle: Optional
   personal_tags:
     - Travel
     - Notes
   ---
   ```

   Align tag names with **`params.personal_tag_nav`** in `hugo.toml` for the Personal topic bar.

**Permalink pattern**: `/personal/:year/:month/:day/:slug/`.

---

## About page

- File: **`content/about-me.md`**
- Rendered with **`layouts/_default/about.html`** (About layout, no “post” chrome if configured that way).

---

## Customisation

### Fonts

1. Set **`site_font`** in `hugo.toml` under `[params]` to a key defined in **`data/site_fonts.yaml`** (e.g. `libre-franklin`, `public-sans`, `source-sans-3`).
2. To add a new face, append an entry in **`site_fonts.yaml`** (`family` + Google Fonts `href`), then reference its key in `hugo.toml`.

The active family is injected as **`--font-sans-primary`** in **`layouts/partials/site-font.html`**; **`static/css/blog.css`** uses it on `html`.

### Topic navigation (Blog / Personal)

- **Blog** topics: `[params.tag_nav]` in `hugo.toml`.
- **Personal** topics: `[params.personal_tag_nav]`.

These control the nav bars on section and listing pages; your post `tags` / `personal_tags` should stay consistent with those labels (or extend the lists).

### Style

- Global rules: **`static/css/blog.css`**.

### Theorem blocks (shortcode)

Statements you want visually distinct (lemmas, definitions in theorem style) can use the **`theorem`** shortcode. It wraps the inner Markdown in a **`div.theorem`**, styled in **`blog.css`** with left padding and a vertical rule so the block reads as a set-apart result.

**Shortcode** (`layouts/shortcodes/theorem.html`):

```
{{% theorem %}}
**Theorem (Name).** *Statement in italics if you like.*

$$
\mathrm{display\ math\ on\ its\ own\ lines}
$$
{{% /theorem %}}
```

**CSS** (`static/css/blog.css`): class **`.theorem`** adds vertical margin, left border, and padding; first/last paragraph margins are tightened; **`.theorem .katex-display`** gets extra vertical spacing for display math.

**Goldmark:** put each **`$$`** delimiter on its own line when display math sits inside the shortcode so the math passthrough extension in `hugo.toml` parses it correctly.

---

## Features (summary)

- **KaTeX** for math (`layouts/partials/math.html`, CSS from CDN in head).
- **Theorem shortcode** for indented statement blocks with a left rule (`layouts/shortcodes/theorem.html`, styles in `static/css/blog.css`).
- **Chroma**-friendly code blocks (if you enable highlighting in `hugo.toml` later).
- **RSS** is disabled (`disableKinds = ['RSS']` in `hugo.toml`); remove or adjust if you want a feed.

---

## License

Site content and templates are yours to license as you prefer. Hugo and third-party assets (e.g. fonts, KaTeX) follow their respective licenses.
