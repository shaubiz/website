# Shaubiz Solutions — Website

Static one-page site. No build step, no dependencies, no third-party requests at runtime.
Everything ships as plain files that GitHub Pages can serve directly.

## Structure

```
index.html          the entire site (HTML + CSS + JS inline — one request)
favicon.svg         browser icon
apple-touch-icon.png  iOS home-screen icon (180×180)
og-image.png        social preview card (1200×630)
img/og-image.svg    source for og-image.png — edit this, then re-render
img/omni.png        Omni partner logo
fonts/*.woff2       self-hosted Inter + Bricolage Grotesque (latin + latin-ext)
robots.txt          crawler rules
sitemap.xml         single-URL sitemap
.nojekyll           tells GitHub Pages to serve files as-is
```

CSS and JS are inlined in `index.html` on purpose: the whole page arrives in one
request, with no flash of unstyled content and nothing to bundle.

## Local preview

```bash
python3 -m http.server 8000
```

Then open <http://localhost:8000>. Use a plain file open (`file://`) only for quick
checks — the self-hosted fonts need a real HTTP origin to load.

## Deploying to GitHub Pages

1. Create a repository on GitHub and push these files to `main` (repo root).
2. **Settings → Pages → Source** → `Deploy from a branch` → `main` → `/ (root)`.
3. Save. The site goes live at `https://<user>.github.io/<repo>/` within a minute.

### Custom domain (`www.shaubiz.com`)

The domain does not point at GitHub yet, so do this **in order** — adding the
`CNAME` file first would make the site unreachable at the `github.io` URL too,
before DNS is ready:

1. Point DNS at GitHub first, following
   [GitHub's custom domain guide](https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site)
   (a `CNAME` record for `www` → `<user>.github.io`).
2. Wait for the DNS change to propagate.
3. Then set the domain under **Settings → Pages → Custom domain**. GitHub commits
   the `CNAME` file for you.
4. Tick **Enforce HTTPS** once the certificate is issued.

## Editing content

- **Contact email** — `info@shaubiz.com` appears three times in `index.html`
  (the link text, its `mailto:`, and the JSON-LD block). Change all three.
- **Absolute URLs** — `https://www.shaubiz.com/` is hardcoded in the canonical
  link, the Open Graph / Twitter tags, and the JSON-LD. Social previews need
  absolute URLs, so update these if the domain changes.
- **Partner logos** — each is an `<li class="partner-logo">` inside
  `#partners-track`. Inline SVG is preferred; the CSS renders every logo as a
  flat white silhouette, so source colours don't matter. JavaScript clones the
  track at runtime to make the marquee loop seamlessly.
- **Footer year** — updates itself from the browser clock.

### Regenerating the social image

Edit `img/og-image.svg`, then:

```bash
rsvg-convert -w 1200 -h 630 img/og-image.svg -o og-image.png
```

The SVG references Inter and Bricolage Grotesque by name, so those fonts need to
be installed locally for the render to match. Without them the shapes still
render correctly but the text falls back to a system font.

## Accessibility & performance notes

Worth preserving if you edit the page:

- Content is visible without JavaScript — the scroll animations are added by JS,
  never baked into the markup.
- All animation is disabled under `prefers-reduced-motion`.
- The mobile menu is a real `<button>` with `aria-expanded`, closing on link
  click, `Escape`, outside click, and viewport resize.
- Fonts are self-hosted, which keeps visitor data out of Google's servers — worth
  keeping for a company based in the EU.
