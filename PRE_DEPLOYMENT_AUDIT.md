# JARVIS/KALKI pre-deployment audit

## Verified source correction

- `backend/server.js` imported `./advanced-intelligence-backend`, but that file is not present in this repository. The repository contains `advanced-intelligence.js`, which exports the `capabilityStatus()` and `preparePlan()` functions used by `server.js`. The import has been corrected to `./advanced-intelligence`.

## Checks performed

- All `backend/*.js` files pass `node --check` under Node.js 22.16.0.
- Backend starts and `GET /api/health` returns `ok: true` after the import correction.
- Desktop-agent Python tests: 13 passed.
- Website static check (`npm run check --prefix website`) passed.
- Website production build was not verified: the dependency installation did not complete, so `vite` was unavailable in this audit environment.

## Deployment blockers / decisions still required

1. Replace the old `jarvis-mobile-edition-alpha.vercel.app` API base in `frontend/script.js`, `frontend/admin.js`, and `frontend/connectors.js` with the final Render service URL.
2. Replace the old `vamshi-vfx.github.io` origin in the desktop-agent allowlist and Android deep-link configuration with the actual GitHub Pages origin and repository path.
3. The frontend sends `/api/command` without authentication. The backend now defaults `ENABLE_PUBLIC_COMMANDS=false`, which blocks `/api/command`, direct skill POST routes, and the YouTube analytics POST route until a real user/session authentication design is implemented. Do not put `JARVIS_API_TOKEN` in browser JavaScript. Keep this flag false on Render for the initial safe deployment.
4. Several admin frontend requests target routes not implemented by this backend. The admin page must be reconciled with the actual server routes before it is published as a functional admin console.
5. Google OAuth token storage, activation keys, and several other states are in process memory. They are not durable across restarts and are not production-ready for multi-instance use.
6. `render.yaml` uses `ALLOWED_ORIGIN` as one origin string. Set it to the final GitHub Pages origin, without a trailing slash or path. Review whether all required origins are supported before using multiple frontends.
7. Android workflow currently creates a debug APK. Release signing and Play Store preparation are separate tasks.
8. The desktop agent is a local companion that runs on the user's computer. It is not a cloud-hosted service.
9. WhatsApp bridge and billing are scaffolding/placeholders, not live production integrations.

## Hosting scope

- Render: Node.js backend only.
- GitHub Pages: static product site and assistant frontend.
- GitHub Actions: Android debug APK build.
- User computer: Python desktop companion.
