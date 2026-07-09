# SwoleMates

Weekly workout planner + tracker for Jason & Shannon. Plan each week per person, log
weights/reps at the gym, see each other's progress, and get suggested working weights
computed from your own logged history (Epley e1RM with a multi-set discount and a
progressive-overload nudge).

**Live:** https://jsunaldo.github.io/swolemates/

## Architecture

- Single-file PWA (`index.html`) — no build step, no frameworks. Charts are inline SVG.
- Data lives in `localStorage` + a Cloudflare Worker (`swolemates-sync.jsunaldo.workers.dev`,
  KV-backed). **No workout data is in this repo** — the repo is just the app shell.
- Sync is entity-level merge (newest `updatedAt` wins per plan/session/exercise), so both
  people can log simultaneously at the gym without clobbering each other.
- All sync requests (reads included) require a shared passphrase header; each device is
  prompted once.
- Three sync blobs: `jason`, `shannon` (plans, sessions, bodyweight) and `shared`
  (exercise library, week templates). The worker keeps an automatic backup ring per blob.

## Deploy

Push to `main` — GitHub Pages serves the repo root. Bump `CACHE_NAME` in `sw.js` when
shipping changes. Worker source lives in `../swolemates-sync/` (deploy with
`npx wrangler deploy`).
