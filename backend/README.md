# MeetingMind Backend

FastAPI service for transcript analysis, persistence, Q&A, and Google Calendar integration.

Full project docs (frontend, end-to-end flow): [`../README.md`](../README.md)

## Run backend only

```bash
cd backend
python -m venv .venv
source .venv/bin/activate   # Windows: .venv\Scripts\activate
pip install -r requirements.txt
python -m spacy download en_core_web_sm
uvicorn main:app --reload --port 8000
```

API docs: `http://127.0.0.1:8000/docs`

## Tech stack (this service)

- **FastAPI**, **psycopg2** (PostgreSQL)
- **Hugging Face Transformers:** **facebook/bart-large-cnn** (summarization pipeline), **distilbert-base-cased-distilled-squad** (extractive Q&A)
- **spaCy** `en_core_web_sm` (action-item heuristics and NLP helpers)
- **Groq** and **Google GenAI** SDKs when API keys are configured

## Analyze path (`/analyze`)

- **Groq** when `GROQ_API_KEY` is set and `GROQ_ANALYZE=1` (default).
- Else **Gemini** when `GEMINI_API_KEY` is set and `GEMINI_ANALYZE=1`.
- Else **local analyze**: **BART-large-CNN** for summaries, **spaCy** for action-item extraction heuristics, and rule-based follow-ups—whenever neither Groq nor Gemini is used for meeting analysis (e.g. no LLM keys or analyze disabled).

`GET /health` describes the active path and loaded models.

## Q&A path (`/ask`)

- **Groq** or **Gemini** when the corresponding API key is configured.
- Else **DistilBERT** extractive QA over the transcript (same model as loaded at startup).

## Google Calendar

In Google Cloud Console: enable **Google Calendar API**, create OAuth **Web client** credentials, and add an authorized redirect URI that matches **`GOOGLE_CALENDAR_REDIRECT_URI`** in `backend/.env` exactly (including `localhost` vs `127.0.0.1`).

Set `GOOGLE_CALENDAR_CLIENT_ID`, `GOOGLE_CALENDAR_CLIENT_SECRET`, and `GOOGLE_CALENDAR_REDIRECT_URI`. After connecting in the UI, you can create events linked to meetings.
