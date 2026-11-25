# TransitAI – Local Public Transportation Information & Route-Assistance System

KLS VDIT, CSE (AI&ML) community project — a full-stack transit assistant for **Hubli-Dharwad** featuring a 3D MapLibre GL JS + deck.gl map, in-process physics simulator with holding strategy, bidirectional & circular route search, ML demand forecasting, OR-Tools fleet dispatch optimization, and an authenticated operator analytics dashboard.

## Architecture

```
Frontend (Next.js 15 + Tailwind v4 + Framer Motion + MapLibre GL JS + deck.gl)
        │  (PWA, English/Kannada i18n, 3D Glassmorphism)
        ▼
Backend (FastAPI + SQLAlchemy + SQLite / PostgreSQL)
  ├── /stops, /stops/nearby, /stops/{id}/eta   (distance/speed/dwell ETA)
  ├── /routes, /routes/{id}, /routes/search     (in-memory graph, 0/1/2 transfers)
  ├── /vehicles                                 (live in-process physics simulator)
  ├── /forecast                                 (scikit-learn Random Forest, auto-trained)
  ├── /optimize                                 (Google OR-Tools mTSP + NN baseline)
  └── /admin/* (X-Admin-Key)                    (analytics, bunching experiment, benchmarks)
```

## Vector Map Tiles (No API Key Required)
TransitAI uses **OpenFreeMap** (`https://tiles.openfreemap.org/styles/dark`) with MapLibre GL JS + deck.gl:
- Free & open-source OpenStreetMap vector tiles with 3D building extrusions
- No API key, registration, or hard request limits (provided as-is; commercial use allowed)
- Includes a **2D Lite** toggle and automatic `prefers-reduced-motion` support

## Quick Start

### Option A: Docker Compose (PostgreSQL + FastAPI Backend)
```powershell
cp .env.example .env
docker compose up --build
```

### Option B: Local Development

#### 1. Backend
```powershell
cd backend
python -m venv venv
venv\Scripts\activate
pip install -r requirements.txt
cp .env.example .env
```

Generate 60 days of synthetic Hubli-Dharwad transit data (15 stops, 3 routes):
```powershell
python ..\data\generate.py
```

Train the Random Forest model (also runs automatically at startup if missing):
```powershell
python ..\ml\train_forecast.py
```

Run the benchmark suite (writes `ml/results.md` and `ml/results.json`):
```powershell
python ..\scripts\benchmark.py
```

Start the FastAPI server:
```powershell
uvicorn app.main:app --reload --port 8000
```

Run backend tests:
```powershell
python -m pytest tests\ -q
```

#### 2. Frontend
```powershell
cd frontend
cp .env.example .env.local
npm install
npm run dev
```

Open:
- Passenger Home & Search: `http://localhost:3000/`
- Route Results & Stop Timeline: `http://localhost:3000/routes`
- 3D Transit Explorer: `http://localhost:3000/map`
- Live Fleet Radar (Simulated): `http://localhost:3000/live`
- Operator Admin Dashboard: `http://localhost:3000/admin` (Default key: `transit-admin-dev-key`)

Run frontend build & tests:
```powershell
npm run build
npm test
```

## Real Benchmark Results (`ml/results.md`)
All metrics below were measured on synthetic Hubli-Dharwad data (`data/generate.py`) and simulated bus runs:

- **Demand Forecast (Random Forest vs Previous-Week Baseline)**:
  - Random Forest Test MAE `6.44` / RMSE `9.96` vs Baseline MAE `7.54` / RMSE `11.68` (`+14.6%` MAE, `+14.7%` RMSE)
- **Fleet Dispatch Optimizer (OR-Tools vs Nearest-Neighbor, 15 stops)**:
  - 2 buses: `69.824 km` vs `73.708 km` (`+5.27%`)
  - 3 buses: `69.824 km` vs `105.007 km` (`+33.51%`)
  - 4 buses: `69.824 km` vs `137.219 km` (`+49.11%`)
- **Holding Strategy (Simulated, 5 seeds `[42, 43, 44, 45, 46]`)**:
  - Without Holding: Mean `6.7 min`, StdDev `5.86 min`, Bunching Events `35`
  - With Holding: Mean `6.38 min`, StdDev `4.8 min`, Bunching Events `31` (`18.1%` StdDev reduction)
- **`/routes/search` Latency (60 stop-pair queries)**:
  - p50: `2.46 ms`, p95: `2.86 ms`, max: `4.72 ms` (`< 1000 ms` PASS)

## Deployment
- **Backend (Render / Railway)**: Use `backend/Dockerfile` (`PORT` env var supported). Set `DATABASE_URL`, `CORS_ORIGINS`, and `ADMIN_API_KEY`.
- **Frontend (Vercel)**: Root directory `frontend/` with `frontend/vercel.json`. Set `NEXT_PUBLIC_API_URL` to your deployed backend URL.
