# MeetingMind

MeetingMind converts meeting transcripts into actionable outputs:
- concise summary
- action items with owners/deadlines
- follow-up suggestions
- searchable meeting history
- Q&A over stored transcripts
- optional Google Calendar integration

The app is split into:
- `backend/` — FastAPI + NLP/LLM orchestration + Postgres persistence
- `frontend/` — React + Vite dashboard UI

## What the app does

### Core features
- Analyze pasted text transcripts or uploaded `.txt` / `.vtt` files
- Generate summary + tasks + follow-ups
- Save meetings and action items in Postgres
- Re-open any past meeting from history
- Ask questions against transcript context (`/ask`)
- Mark action items complete/incomplete
- Optionally create and link Google Calendar events

### Output flow (high-level)
1. User submits transcript (paste or upload).
2. Backend preprocesses text and chooses analyze backend:
   - Groq if configured and enabled
   - else Gemini if configured and enabled
   - else local fallback (if allowed)
3. Backend returns summary/action items/follow-ups and stores records.
4. Frontend renders results board, history, task controls, and Q&A panel.

## Tech stack

- **Backend:** FastAPI, psycopg2, spaCy, transformers, Groq SDK, Google GenAI SDK
- **Frontend:** React, Vite, Axios, Framer Motion, Tailwind (via Vite plugin)
- **Database:** PostgreSQL

## Prerequisites

- Python 3.10+ (recommended 3.11/3.12)
- Node.js 18+ and npm
- PostgreSQL running locally (or reachable via `DATABASE_URL`)

## Quick start (local dev)

### 1) Backend setup

```bash
cd backend
python -m venv .venv
source .venv/bin/activate   # Windows: .venv\Scripts\activate
pip install -r requirements.txt
python -m spacy download en_core_web_sm
```

Create `backend/.env`:

```env
# Required
DATABASE_URL=postgresql://postgres:postgres@localhost:5432/meetingmind

# Recommended (for best analyze quality)
GROQ_API_KEY=your_key_here

# Optional fallback
# GEMINI_API_KEY=your_key_here

# Optional behavior flags
# GROQ_ANALYZE=1
# GEMINI_ANALYZE=1
# ALLOW_LOCAL_ANALYZE_WITHOUT_LLM=0
```

Run backend:

```bash
uvicorn main:app --reload --port 8000
```

API docs: `http://127.0.0.1:8000/docs`

### 2) Frontend setup

```bash
cd frontend
npm install
npm run dev
```

Frontend runs at `http://127.0.0.1:5173` (Vite default in this project).
In dev, frontend proxy maps `/api/*` to backend `http://127.0.0.1:8000/*`.

## How to use the app

1. Open the frontend.
2. Enter a meeting title.
3. Paste transcript text or upload `.txt`/`.vtt`.
4. Click analyze.
5. Review summary, tasks, follow-ups.
6. Toggle task completion status.
7. Ask follow-up questions in Q&A panel.
8. Reopen older meetings from history.

## Backend endpoints (main)

- `GET /health` — runtime/config status
- `POST /analyze` — analyze pasted transcript JSON
- `POST /analyze/upload` — analyze uploaded file
- `POST /ask` — Q&A for a saved meeting
- `GET /meetings` — list recent meetings
- `GET /meeting/{meeting_id}` — load one meeting + action items
- `PATCH /action-items/{action_id}` — mark completed/uncompleted
- `DELETE /meeting/{meeting_id}` — delete meeting and related data

## Google Calendar (optional)

If you want calendar linking:
- set `GOOGLE_CALENDAR_CLIENT_ID`
- set `GOOGLE_CALENDAR_CLIENT_SECRET`
- set `GOOGLE_CALENDAR_REDIRECT_URI`
- ensure redirect URI exactly matches Google Cloud Console OAuth settings

Once connected in UI, you can create a calendar event from meeting context.

## Common troubleshooting

- **`spaCy model missing`**
  - Run: `python -m spacy download en_core_web_sm`
- **Frontend cannot reach API**
  - Ensure backend is running on port `8000`
  - Ensure frontend is running with `npm run dev`
- **Analyze quality poor / disabled**
  - Verify `GROQ_API_KEY` (or `GEMINI_API_KEY`) in `backend/.env`
  - Check `GET /health` output
- **Google OAuth redirect_uri_mismatch**
  - Copy `GOOGLE_CALENDAR_REDIRECT_URI` exactly into Google OAuth client config
  - `localhost` and `127.0.0.1` are treated as different URIs

## Project structure

```text
MeetingMind/
  backend/
    main.py
    google_calendar.py
    requirements.txt
  frontend/
    src/
    package.json
```
