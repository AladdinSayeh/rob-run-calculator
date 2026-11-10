# ROB RUN Calculator — React + Vite (no backend)

This version is a static React app. All calculations, fuse optimization and base link sharing run entirely in the browser. **No Render service, MongoDB, API URL, credentials or account are required.**

## Local development

```bash
cd frontend
npm install
npm run dev
```

Production build:

```bash
cd frontend
npm run build
```

Vite writes minified static assets to `frontend/dist/`. Source maps are disabled. This is obfuscation, not encryption: JavaScript delivered to browsers can be inspected.

## Publishing on GitHub Pages

The root `index.html` is the existing legacy site, preserved until React is deployed. To publish React to the same URL:

1. Open **Repository Settings → Pages → Build and deployment → Source** and choose **GitHub Actions** instead of **Deploy from a branch**.
2. Open **Actions → Deploy React frontend to GitHub Pages (manual cutover)** and click **Run workflow** on `main`.
3. When deployment succeeds, open https://aladdinsayeh.github.io/rob-run-calculator/ and hard-refresh.
4. Rollback: **Settings → Pages → Deploy from a branch → main → /(root)**. The legacy root HTML is still present.

The workflow builds the React frontend and uploads `frontend/dist` as a Pages artifact. The app's Vite base path is `/rob-run-calculator/`.

## Features

- Responsive blue-and-gold ROB RUN-inspired interface with three tabs.
- Single-building defence/raid calculator.
- Multi-structure base builder and eviction estimate, rounding decoder counts for each structure independently.
- Fuse cost optimizer for target defence or budget.
- Shareable base configurations using URL fragments, without a database.
- English interface with optional Arabic translation of raid/eviction explanation and footer.

**Privacy:** A share link encodes the full base configuration in its URL. Anyone who has the link can read it. No data is stored on a server.

**Limitations:** This is an unofficial fan-made tool, not affiliated with Kefir Games. The optimizer assumes mixed fuse rarities are permitted. Calculations reflect the existing game guide and may need adjustment after updates. The share link format differs from the old site's links.
