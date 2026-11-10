# ROB RUN Calculator — staged MERN migration

The existing root `index.html` is intentionally unchanged and remains the active GitHub Pages website. The React frontend and Express/MongoDB API live in separate directories. **Do not switch GitHub Pages to GitHub Actions until the backend is deployed and verified.**

## Architecture

- `frontend/`: React + Vite, compiled to static HTML/CSS/minified JavaScript for GitHub Pages. No production source maps.
- `backend/`: Express/Node API with validated calculations and optimization. `POST /api/calculate`, `POST /api/optimize`, `GET /api/buildings`, `POST /api/bases`, `GET /api/bases/:id`, `GET /api/health`.
- MongoDB Atlas: saved base configurations with a 90-day TTL. The database is optional for calculations but required to save/load shareable base links.
- `render.yaml`: Render Blueprint for the API. `.github/workflows/deploy-react.yml`: manual production cutover workflow.

## Local development

Install Node.js 20 or newer. In one terminal:

```sh
cd backend
npm install
cp .env.example .env
# Set MONGODB_URI in backend/.env if saving bases is required.
npm run dev
```

In another terminal:

```sh
cd frontend
npm install
cp .env.example .env.local
# Set VITE_API_URL=http://localhost:10000 in frontend/.env.local
npm run dev
```

The frontend calls the API for calculations. It will display an API configuration error if the backend URL is not configured. MongoDB Atlas is not necessary for calculating, but share links require it.

## Deploy safely without interrupting the old site

1. Create a MongoDB Atlas project, cluster, database user and network access rule. Use a dedicated user with least privileges. Copy its connection URI, but **never commit credentials**.
2. Deploy the backend on Render using the repository's `render.yaml` Blueprint (or create a Node web service rooted at `backend`). Configure `MONGODB_URI` as a **secret environment variable**. `FRONTEND_ORIGIN` defaults to `https://aladdinsayeh.github.io`. Render provides `PORT` automatically.
3. Check `https://YOUR-RENDER-SERVICE.onrender.com/api/health` and confirm the API responds. Test `POST /api/calculate` and saving a base.
4. In GitHub repository **Settings → Secrets and variables → Actions → Variables**, create `VITE_API_URL` set to the Render HTTPS origin (no trailing slash). This is a public frontend build setting, **not a secret**.
5. To switch the site, in **Settings → Pages → Build and deployment**, change source from **Deploy from a branch** to **GitHub Actions**. Then manually run **Actions → Deploy React frontend to GitHub Pages (manual cutover) → Run workflow**. This replaces the published frontend only when you intentionally cut over.
6. To roll back, switch Pages source back to **Deploy from a branch**, branch `main`, folder `/(root)`. The original `index.html` remains in place.

The existing legacy site's `#base=` encoded links are not automatically migrated into MongoDB. New React links use MongoDB-generated IDs. The frontend currently offers a text-based building selector; illustrated legacy building cards can be ported as a separate UI enhancement.

## Source code visibility and private repository

- **No encryption can make downloaded frontend JavaScript uncopyable.** Vite minifies production bundles and disables source maps, but browser code remains inspectable.
- Game formulas and optimization are implemented in `backend/src/game.js` and are not shipped with the React bundle. API outputs can still be studied or reverse engineered.
- To make this repository private: **GitHub → Repository → Settings → General → Danger Zone → Change repository visibility → Make private**. Check your GitHub plan's Pages eligibility first; private repositories may have plan-dependent Pages limitations. GitHub Pages still serves the built frontend publicly if the site is public.
- A private repo can be connected to Render using authorized GitHub access. For tighter separation, move the backend into its own private repository and deploy it independently. Keeping the frontend and backend together in a public repository exposes the backend source even if the backend executes only on Render.
- Do not store Atlas passwords, API keys, or secrets in `VITE_*` variables, JavaScript, workflow files, or commits. If a secret was ever committed, rotate it.

## Known scope

- Current gameplay rules are based on the existing calculator and should be checked against game updates.
- The optimizer assumes arbitrary mixes of fuse rarities are allowed and excludes research costs.
- Eviction time is an estimate of installation only, not the full in-game eviction process.
- No user authentication: anyone with a saved-base link can view it. Saved bases expire after 90 days.
