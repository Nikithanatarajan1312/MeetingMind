# MeetingMind Backend

FastAPI service for transcript analysis, persistence, Q&A, and calendar integration.

For complete project docs (features, execution flow, setup for backend + frontend), see:
- [`../README.md`](../README.md)

## Run backend only

```bash
cd backend
python -m venv .venv
source .venv/bin/activate   # Windows: .venv\Scripts\activate
pip install -r requirements.txt
python -m spacy download en_core_web_sm
uvicorn main:app --reload --port 8000
```

Open docs at:
- `http://127.0.0.1:8000/docs`

## Analyze backend selection

- Groq is used when `GROQ_API_KEY` is present and `GROQ_ANALYZE=1`.
- Otherwise Gemini is used when `GEMINI_API_KEY` is present and `GEMINI_ANALYZE=1`.
- Local fallback is used only when allowed by env toggles.

## Google Calendar OAuth notes

In Google Cloud Console:
- enable Google Calendar API
- create OAuth Web Client credentials
- set authorized redirect URI exactly equal to `GOOGLE_CALENDAR_REDIRECT_URI`

If you see `redirect_uri_mismatch`, compare values character-for-character (`localhost` vs `127.0.0.1` also matters).
