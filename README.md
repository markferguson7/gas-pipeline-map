# Natural Gas Pipelines — Map

Two static files, no backend:

- `index.html` — the map app (MapLibre GL + PMTiles)
- `pipelines.pmtiles` — all 32,851 pipeline segments / 229 operators (~8.7 MB)

The app streams only the tiles in view from the `.pmtiles` file using HTTP range
requests, so the full dataset stays fast and cheap to host.

## Deploy to GitHub Pages

1. Create a repo and add both files to the root (keep them side by side).
2. Commit and push.
3. Repo **Settings → Pages → Build and deployment**: Source = *Deploy from a branch*,
   pick your branch and `/ (root)`, Save.
4. Wait ~1–2 minutes. Your map is live at `https://<user>.github.io/<repo>/`.

GitHub Pages supports range requests, so PMTiles works with no extra config.

## Preview locally first

Your browser blocks `pipelines.pmtiles` when you open `index.html` directly
(`file://`), and Python's `http.server` does **not** support range requests.
Use a server that does:

```bash
cd <folder-with-the-two-files>
npx serve         # then open the printed http://localhost:3000
```

## Moving to Cloudflare later

Same two files. Put `pipelines.pmtiles` on R2 (or Pages) and host `index.html`
on Pages. If the data lives on a different domain than the app, enable CORS on
the bucket for your site's origin. No code change needed otherwise.

## Updating the data

The `.pmtiles` file is generated from the source GeoJSON. When the data changes,
regenerate it (e.g. with `tippecanoe -zg -o pipelines.pmtiles -l pipelines
source.geojson`) and replace the file. The layer name must stay `pipelines` and
each feature should carry `op`, `type`, and `mi` properties.
