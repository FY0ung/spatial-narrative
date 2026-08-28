# Spatial Narrative Interface (SNI)

A one-page reference connecting Spatial Narrative Interface patterns with implementation
approaches and the open-source geospatial stack, plus four working demos built on real
GISTDA data (land use, LandTrendr forest change, flood extent, B.E. 2567).

Static site — no build step, no dependencies, no framework.

## Structure

```
.
├── index.html                  # the one-pager (73 KB)
├── 404.html
├── favicon.svg
├── apple-touch-icon.png
├── og-cover.png                # 1200×630 social card
├── robots.txt
├── .nojekyll                   # serve files as-is, skip Jekyll
└── demos/
    ├── landx-insight.html                       # p1 · Automated Insight
    ├── storymap.html                            # p2 · Guided Narrative
    ├── spatial-indicator-dashboard-gistda.html  # p3 · Spatial Indicator Dashboard
    └── spatial-chat-v2.html                     # p4 · Conversational Interface
```

Each demo is a self-contained HTML file: its own CSS, JS, data and icon sprite inline.
Open any of them directly — they work from `file://` as well as over HTTP.

`index.html` maps `p1`–`p4` to file names in one place:

```html
<script>
  window.DEMOS = {
    "p1": "landx-insight.html",
    "p2": "storymap.html",
    "p3": "spatial-indicator-dashboard-gistda.html",
    "p4": "spatial-chat-v2.html"
  };
</script>
```

The loader at the bottom of `index.html` points each preview `<iframe>` and its
"open live demo" link at `demos/<file>`. To swap a demo, replace the file and update
that map — nothing else references the names.

## Run locally

```bash
python3 -m http.server 8000
# → http://localhost:8000
```

Opening `index.html` straight off disk works too, but a server matches production.

## Deploy · GitHub Pages

1. Push this folder to a repository.
2. **Settings → Pages → Build and deployment → Source: Deploy from a branch**,
   branch `main`, folder `/ (root)`.
3. The site goes live at `https://<username>.github.io/<repo>/` in a minute or two.

Everything is relative-path, so it works from a sub-path without changes.

### Adding a custom domain later

1. Create a `CNAME` file at the repo root containing only the host, e.g. `sni.example.com`.
2. DNS: `CNAME` record → `<username>.github.io` (or four `A` records to GitHub's
   apex IPs if you use the bare domain).
3. **Settings → Pages → Custom domain**, then tick **Enforce HTTPS** once the
   certificate is issued.
4. In `index.html`, make the social tags absolute — some scrapers do not resolve
   relative ones:

   ```html
   <meta property="og:url"   content="https://sni.example.com/">
   <meta property="og:image" content="https://sni.example.com/og-cover.png">
   <meta name="twitter:image" content="https://sni.example.com/og-cover.png">
   <link rel="canonical" href="https://sni.example.com/">
   ```

## Data and credentials

The demos read live vector tiles from GISTDA / Vallaris. Two `api_key` values are
embedded in the tile URLs inside each demo file — unavoidable for client-side map
tiles, and readable by anyone who views source on the deployed site.

If those keys support referrer or domain restriction, lock them to the deployed
host. Rotating a key means editing the tile URLs in all four files under `demos/`.

The LandTrendr raster layer needs a 60-minute JWT that a static page cannot mint, so
each demo ships a baked-in offline version of the 2567 layer (real measured counts,
approximate point positions) and works with no token at all. Pasting a fresh token
into the layer panel switches to the live raster.

Basemap tiles come from GISTDA's public OpenMapTiles service — no key required.

## Notes

- Fonts load from Google Fonts; everything else is inline.
- `storymap.html` is 3.5 MB (embedded geometry) and loads only when its preview
  scrolls into view, or when opened directly.
- Demo UI is Thai with an EN toggle; the one-pager is English.
