# TransitAI Implementation Plan (`PLAN.md`)

Tracks the MVP build order from `SPEC.md`:

- [x] **1. Repo setup, `CLAUDE.md`, environment**
- [x] **2. Data: synthetic generator, DB schema, seed script** (`data/generate.py`, `backend/app/models.py`)
- [x] **3. Backend: routes, stops, vehicles endpoints** (bidirectional, circular, multi-transfer search in `< 1s`)
- [x] **4. Vehicle simulator** (in-process physics simulator with holding strategy & distance/speed/dwell ETA)
- [x] **5. Forecast model: train, evaluate, expose `/forecast`** (`ml/train_forecast.py`, Random Forest vs previous-week baseline)
- [x] **6. Optimizer: implement, benchmark, expose `/optimize`** (OR-Tools mTSP vs nearest-neighbor baseline)
- [x] **7. Frontend: search, route map, live tracking** (Next.js + Tailwind)
- [x] **8. Admin dashboard & hardening** (grouped SQL, CORS env, Admin API key auth, Docker Compose, benchmarks)
- [ ] **9. 3D Map upgrade (MapLibre GL JS + deck.gl)** (`feat/3d-map`)
- [ ] **10. 3D Glassmorphism UI, Framer Motion, Kannada i18n, PWA** (`feat/ui-3d-polish`)
- [ ] **11. Admin what-if holding controls, dispatch overlay, CSV export & deployment** (`feat/admin-deploy`)
