# Local Public Transportation Information and Route-Assistance System

KLS VDIT, CSE (AI&ML) community project. This is the MVP spec.

## 1. Problem
Local bus passengers face erratic waiting times (bunching and gapping), poor route information, and no demand-aware scheduling. Operators lack tools to forecast demand and dispatch efficiently.

## 2. Users
- **Passenger:** finds routes and stops, sees live or simulated ETAs.
- **Operator/Admin:** monitors demand, bunching, and fleet dispatch.

## 3. MVP Features (in scope)
1. **Route and stop search:** origin to destination, with stop list and route map.
2. **Live bus tracking:** simulated GPS feed (or GTFS-realtime if available) with ETA per stop.
3. **Demand forecasting:** LSTM or Random Forest predicting boardings per stop per time slot.
4. **Route/dispatch optimization:** TSP/mTSP heuristic (OR-Tools or genetic algorithm) for fleet dispatch.
5. **Admin dashboard:** demand heatmap, headway/bunching chart, forecast vs actual.

## 4. Out of Scope (documented as future work)
- Real Cloud-Fog-Edge deployment and V2I/V2V hardware
- Real federated learning (describe the architecture; optionally simulate with 2-3 clients)
- Biometric/RFID fare collection
- Native mobile apps

## 5. Architecture
- **Frontend:** Next.js, Tailwind, Leaflet or Mapbox
- **Backend:** FastAPI (Python)
- **DB:** PostgreSQL
- **ML:** scikit-learn / PyTorch, models served via the FastAPI backend
- **Optimization:** Google OR-Tools
- **Data:** GTFS static feed or synthetic generator in `/data/generate.py`

```
Frontend (Next.js) -> FastAPI -> PostgreSQL
                         |-> /forecast (ML model)
                         |-> /optimize (TSP/mTSP solver)
                         |-> /vehicles (simulated GPS)
```

## 6. Data Model (draft)
- `stops(id, name, lat, lng)`
- `routes(id, name)`, `route_stops(route_id, stop_id, seq)`
- `trips(id, route_id, start_time)`
- `vehicle_positions(id, trip_id, lat, lng, ts)`
- `boardings(stop_id, ts, count)`

## 7. API (draft)
- `GET /routes?from=&to=`
- `GET /stops/{id}/eta`
- `GET /vehicles`
- `GET /forecast?stop_id=&horizon=`
- `POST /optimize` (stops, fleet size) returns ordered routes and cost

## 8. Success Metrics
- Forecast: MAE/RMSE vs a naive baseline (previous-week average)
- Optimization: total route distance vs a nearest-neighbor baseline
- Bunching: headway variance with and without the holding strategy (simulated)
- App: search returns in under 1 second

## 9. Task Plan (build in this order)
1. Repo setup, `CLAUDE.md`, environment
2. Data: GTFS import or synthetic generator, DB schema, seed script
3. Backend: routes, stops, vehicles endpoints
4. Vehicle simulator (moves buses along routes, creates bunching)
5. Forecast model: train, evaluate, expose `/forecast`
6. Optimizer: implement, benchmark, expose `/optimize`
7. Frontend: search, route map, live tracking
8. Admin dashboard
9. Tests, README, deployment (Vercel + Render/Railway)
10. Report and slides: results, architecture diagram, future scope

## 10. Team Split (suggested for 4 people)
- Data + backend
- ML forecasting
- Optimization + simulator
- Frontend + design (Stitch)
