# CLAUDE.md: Project Rules

Project: Local Public Transportation Information and Route-Assistance System (KLS VDIT, CSE AI&ML).
Read `SPEC.md` for scope and `PLAN.md` for the current task list before starting any work.

## Stack
- Frontend: Next.js (TypeScript), Tailwind CSS, Leaflet + OpenStreetMap tiles (no paid map keys)
- Backend: FastAPI (Python 3.11+), SQLAlchemy, PostgreSQL
- ML: scikit-learn, PyTorch (only if needed)
- Optimization: Google OR-Tools
- Tests: pytest (backend), Vitest or Playwright (frontend)

## Folder Structure
```
/frontend      Next.js app
/backend       FastAPI app (app/, tests/)
/ml            training scripts, saved models, results.md
/data          GTFS files, synthetic data generator
/docs          architecture diagram, report material
/docs/design   Stitch exports (code.html + screen.png per screen)
```

## Design Source of Truth
- Screens live in `/docs/design/<screen>/code.html` and `screen.png`: home_search, route_result, live_tracking, transit_operator_admin_dashboard.
- **3D Redesign Approved**: 60-30-10 palette — 60% Ricotta Cream (`#faf6ef`, `#f2eadc`), 30% Prussian Blue (`#003153`, `#0a3d62`), 10% Lobster Red (`#d64527`), using 3D glassmorphism cards, layered shadows, and tilt-on-hover interactions plus a 3D MapLibre GL JS + deck.gl map (with a flat 2D Lite mode fallback).
- Tokens: surface `#faf6ef` (Ricotta Cream), surface-container-low `#f7f1e6`, surface-container `#f2eadc`, surface-container-highest `#ddd0bd`, primary `#003153` (Prussian Blue), secondary `#d64527` (Lobster Red), tertiary `#e05336`.
- Map stack: MapLibre GL JS + deck.gl with free OpenFreeMap vector tiles (no API key). Respect `prefers-reduced-motion`.
- Icons: Material Symbols Outlined. Mobile-first for passenger screens; desktop layout for the admin dashboard.
- Convert Stitch HTML into reusable React components (e.g. SearchCard, NearbyStopCard, BottomNav, StopTimeline, KpiCard). Do not paste the HTML as-is.
- Replace all hardcoded demo data with API calls. Use local place names (configurable), not the sample city names, and no hardcoded user names.
- Do not build features that appear in the designs but are out of scope (payments, passes, biometrics). Hide them or label them "Coming soon".

## Working Rules
1. Do **one task at a time** from `PLAN.md`. Do not start the next task unprompted.
2. Before coding, state a short plan (files to change, approach). Ask if requirements are unclear.
3. Keep changes small and focused. No unrelated refactors.
4. After each change, run the relevant tests or app and report the result honestly. If you could not verify something, say so.
5. Never invent data, metrics, or results. Report real numbers from real runs, and always include the baseline. Any dashboard number must come from real model output or be clearly labeled "simulated".
6. Do not add new dependencies without saying why.
7. Never commit secrets. Use `.env` and keep `.env.example` updated.
8. One feature per commit, with a clear message (`feat:`, `fix:`, `docs:`).
9. Work on your own branch (`feat/<area>-<task>`), never directly on `main`.

## Code Conventions
- Python: type hints, small functions, docstrings on public functions, format with `ruff`/`black`
- TypeScript: strict mode, functional components, no `any`
- API: REST, JSON, consistent error shape `{ "error": "message" }`
- Validate all inputs with Pydantic models
- Config (city name, map center, API URL) goes in env/config files, not in components

## ML Conventions
- Fixed random seeds. Split train/validation/test by time, not randomly, for forecasting.
- Always compare against a naive baseline (previous-week average).
- Save models to `/ml/models/` and log metrics (MAE, RMSE) in `/ml/results.md`.

## Optimization Conventions
- Implement a nearest-neighbor baseline first, then OR-Tools or a genetic algorithm.
- Report total distance/cost of both on the same instances.

## Out of Scope (do not build)
- IoV/V2I hardware, real Cloud-Fog-Edge deployment
- Real federated learning (simulate with 2-3 clients only if asked)
- RFID/biometric fare collection, payments
- Native mobile apps

## Don'ts
- Don't rewrite working code just for style
- Don't skip tests to finish faster
- Don't claim something works unless it was run
- Don't change the design tokens or the spec without asking

## When Stuck
Report the exact error, the file and line, and what you already tried. Propose 1-2 fixes before changing anything.
