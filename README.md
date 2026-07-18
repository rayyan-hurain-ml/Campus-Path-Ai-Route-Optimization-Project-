# CampusPath AI

## Summary

CampusPath AI is a full-stack portfolio project that solves three real university routing and scheduling problems in the browser. The interface includes a sidebar navigation rail, an in-app **Guide & Help** section, interactive campus grid maps, performance charts, and plain-English result explanations. The Python FastAPI backend runs five AI algorithms from scratch: **BFS**, **DFS**, **A\***, a **Genetic Algorithm** for multi-stop delivery, and **CSP backtracking** for conflict-free timetables. The React dashboard visualizes paths, routes, metrics, and schedules in real time.

## Technologies

- **Frontend**: HTML, CSS, JavaScript ecosystem via **React 19** and **TypeScript**, bundled with **Vite**, styled with **Tailwind CSS**, charts with **Recharts**
- **Backend**: **Python 3.11+**, **FastAPI**, **Uvicorn**, and **Pydantic** for request/response schemas
- **AI algorithms**: Pure Python implementations (no heavy ML model files) under `backend/app/algorithms/`
- **Tooling**: npm, pip; optional deploy configs for **Render** (API) and **Vercel** (frontend)

## Features

- **Pathfinding Lab** — draw obstacles on a 2D grid, set start/end, compare BFS vs DFS vs A* on the same map
- **Delivery Optimizer** — place multi-stop delivery points and evolve a shorter visit order with a Genetic Algorithm
- **Timetable Scheduler** — assign courses to rooms and time slots with CSP backtracking (no teacher/room conflicts)
- Interactive grid visualization with visited cells, final path, and KPI cards (path length, nodes expanded, time)
- Side-by-side metrics charts for algorithm comparison
- Automatic **In Simple Words** explanations after each run, plus optional technical details
- Built-in **Guide & Help** tab covering what/why/how for every module
- Sample campus map and timetable presets for quick demos
- Health check on the API for quick diagnostics (`GET /api/health`)

## Algorithms

| # | Algorithm | Course topic | File | Use case |
|---|-----------|--------------|------|----------|
| 1 | **BFS** | Uninformed search | `backend/app/algorithms/bfs.py` | Shortest path on an unweighted grid |
| 2 | **DFS** | Uninformed search | `backend/app/algorithms/dfs.py` | Deep exploration / maze-style search |
| 3 | **A\*** | Informed heuristic search | `backend/app/algorithms/astar.py` | Optimal path with fewer expanded nodes |
| 4 | **Genetic Algorithm** | Evolutionary optimization | `backend/app/algorithms/genetic_algorithm.py` | Multi-stop delivery route (TSP-style) |
| 5 | **CSP Backtracking** | Constraint satisfaction | `backend/app/algorithms/csp_timetable.py` | Conflict-free class timetable |

## Project structure

```
campuspath-ai/
├── backend/                      # FastAPI + all AI algorithms
│   ├── app/
│   │   ├── algorithms/           # bfs, dfs, astar, GA, CSP, graph utils
│   │   ├── api/routes.py         # REST endpoints
│   │   ├── models/schemas.py     # Pydantic request/response models
│   │   └── main.py               # App entry + CORS
│   ├── requirements.txt
│   ├── .env.example
│   └── render.yaml               # Optional Render deploy config
├── frontend/                     # React + Vite dashboard
│   ├── src/
│   │   ├── pages/                # Pathfinding, Delivery, Timetable, Guide
│   │   ├── components/           # MapGrid, charts, explanations, UI
│   │   ├── api/client.ts         # HTTP client → FastAPI
│   │   └── App.tsx               # Layout + navigation
│   ├── package.json
│   ├── .env.example
│   └── vercel.json               # Optional Vercel deploy config
└── README.md
```

## What I learned (process)

- Splitting **UI visualization**, **REST orchestration**, and **algorithm modules** keeps the repo small while still showing a realistic service boundary
- Implementing search and optimization **from scratch** forces clear thinking about queues, heuristics, fitness, and backtracking
- **Controlled experiments** (same grid, same start/end) make BFS vs DFS vs A* comparisons honest and easy to explain
- **Plain-English result panels** help non-technical viewers understand outputs without reading code
- Separating frontend and backend ports (5173 / 8000) mirrors how real apps talk over HTTP/JSON

## Overall growth

- Practiced end-to-end ownership from interactive UI to algorithm correctness and API design
- Reinforced measuring algorithms with concrete metrics (path length, nodes expanded, wall-clock time)
- Balanced ambition with maintainability: five algorithms, three modules, one coherent campus story

## How this project can be improved

- Add path animation / step-through playback for search expansion order
- Support weighted grids (stairs, congestion) so A* heuristics matter even more
- Persist custom maps and timetables with SQLite instead of in-memory presets only
- Add automated tests (API integration tests, React component tests)
- Containerize with Docker Compose for one-command setup across operating systems
- Deploy frontend + API with a production CORS and environment setup (Render + Vercel configs are already sketched)

## Running the project

After cloning:

1. Start the **backend** on port **8000**.
2. Start the **frontend** on port **5173**.
3. Open **http://localhost:5173** and use **Guide & Help** if you need a walkthrough.

### Prerequisites

- **Python 3.11+** on your PATH
- **Node.js 18+** (LTS recommended) and npm

### Terminal method (step by step)

You need **two** terminal tabs or windows. From the **repository root** (the folder that contains `backend` and `frontend`):

**1) Backend — AI API (Python / FastAPI)**

```bash
cd backend
pip install -r requirements.txt
uvicorn app.main:app --reload --host 127.0.0.1 --port 8000
```

Leave this window running. Interactive API docs: **http://localhost:8000/docs**

**2) Frontend — React / Vite**

Open a **new** terminal:

```bash
cd frontend
npm install
npm run dev
```

Leave this window running. Vite prints a local URL (usually **http://localhost:5173**). Open that URL in your browser.

**Summary**

| Piece        | Port | Folder     | Role                                      |
|-------------|------|------------|-------------------------------------------|
| FastAPI API | 8000 | `backend`  | Pathfinding, delivery GA, CSP timetable   |
| Vite client | 5173 | `frontend` | Dashboard UI in the browser               |

### "Failed to fetch" versus algorithm errors

These are **two different problems**:

| Symptom | Meaning | Fix |
|--------|---------|-----|
| **Failed to fetch** / network error in the UI | The **browser cannot talk to the FastAPI backend** on port **8000**. | Run `cd backend && uvicorn app.main:app --reload --port 8000`. Open `http://127.0.0.1:8000/api/health` — you should see JSON with `"status":"ok"`. Run the Vite app from `frontend` and use `http://localhost:5173`, not a `file://` link. |
| UI loads but path/route/timetable fails | Bad input or an algorithm edge case (blocked start/end, too few stops, over-constrained CSP). | Use the sample buttons (**Load CASE Campus**, **Load Demo Stops**, **Load CASE Islamabad Sample & Solve**), then retry. Check API docs at `/docs` if needed. |

**Summary:** You always need the **backend running locally** for Compare All / GA / CSP to work. The frontend alone cannot run the AI algorithms.

### How to use each module

**Pathfinding Lab**

1. Click **Load CASE Campus** for a sample map with walls, or draw obstacles yourself.
2. Set **Start** (green) and **End** (goal).
3. Click **Compare All** to run BFS, DFS, and A* together.
4. Read KPI cards, the chart, and the automatic explanation.

**Delivery Optimizer**

1. Click **Load Demo Stops** or click the grid to add stops (first stop = depot).
2. Adjust population, generations, and mutation if you want.
3. Click **Run Genetic Algorithm** and compare optimized distance vs naive order.

**Timetable Scheduler**

1. Click **Load CASE Islamabad Sample & Solve**.
2. Inspect the timetable grid and satisfied constraints list.

### Optional configuration

- Backend CORS: set `CORS_ORIGINS` if you deploy the frontend to another domain (see `backend/.env.example`).
- Frontend API URL: set `VITE_API_URL` when the API is not on `http://localhost:8000` (see `frontend/.env.example`).

### API endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/health` | Health check |
| POST | `/api/pathfind` | Run BFS / DFS / A* / all |
| POST | `/api/delivery/optimize` | Genetic Algorithm delivery route |
| GET | `/api/timetable/sample` | Sample timetable solve |
| POST | `/api/timetable/solve` | Custom CSP timetable input |


## License

Academic / MIT-style — use and adapt freely for learning and portfolio demos.
