# AI Stock Analysis

A chat-based stock analysis assistant. A FastAPI backend wraps a LangChain agent (via [Thesys C1](https://thesys.dev)) with live market-data tools, and a React frontend renders the conversation as generative UI using Thesys's C1 chat components.

**Live app:** https://ai-stock-analysis-eight.vercel.app/

## Features

The agent can answer questions about a stock using these tools:

- **Current price** — latest closing price for a ticker
- **Historical prices** — price history over a given date range
- **Balance sheet** — company balance sheet data
- **News** — recent news for a ticker

Market data comes from [yfinance](https://pypi.org/project/yfinance/); the agent itself runs on Thesys C1's OpenAI-compatible generative UI model.

## Project structure

```
backend/    FastAPI server exposing POST /api/chat (streams agent responses)
frontend/   Vite + React app (Thesys C1Chat UI), proxies /api to the backend
```

## Getting started

### Backend

```bash
cd backend
python -m venv .venv
.venv\Scripts\activate        # Windows
pip install -r requirements.txt
```

Create a `.env` file in `backend/` (see `.env.example`):

```
OPENAI_API_KEY="your-thesys-c1-api-key-here"
```

Run the server:

```bash
python main.py
```

The API listens on `http://localhost:8888`.

### Frontend

```bash
cd frontend
npm install
npm run dev
```

The dev server runs on `http://localhost:3000` and proxies `/api` requests to the backend on port 8888.

## Deployment

Backend on [Render](https://render.com), frontend on [Vercel](https://vercel.com).

- Frontend: https://ai-stock-analysis-eight.vercel.app/
- Backend: https://ai-stock-analysis-kw6s.onrender.com

Note: the Render free tier spins down after inactivity, so the first request after idle can take 15–50s to respond.

### Backend (Render)

1. New **Web Service** → connect this GitHub repo → Render detects `render.yaml` at the repo root.
2. Set the `OPENAI_API_KEY` env var (your Thesys C1 key) in the service's Environment settings — it's marked `sync: false` so Render will prompt for it rather than reading it from the repo.
3. Deploy. Backend is live at `https://ai-stock-analysis-kw6s.onrender.com` (update `frontend/vercel.json`'s rewrite destination if the service is ever recreated with a different URL).

### Frontend (Vercel)

1. New Project → import this repo → set **Root Directory** to `frontend`.
2. Vercel picks up `frontend/vercel.json`, which builds the Vite app and rewrites `/api/*` to the Render backend (avoids CORS issues in production).
3. Deploy.
