# SerenityAI

An AI-powered mental wellbeing companion that combines emotion detection, voice journaling, and LLM-driven insights to track and improve user wellbeing over time.

## What it does

- **Text & voice journaling** — submit journal entries via text or audio; the backend transcribes audio (AssemblyAI), runs emotion classification (DistilBERT), and stores a wellbeing score
- **Multi-modal emotion analysis** — combines audio-based emotion detection (Hatman model + OpenSMILE acoustic features) with text-based classification
- **AI chat** — conversational interface powered by Gemini that has context of your journal history
- **Follow-up questions** — Gemini generates personalised follow-up questions based on journal entries
- **Activity impact tracking** — correlates logged activities with wellbeing scores over time
- **Dashboard** — Next.js UI with weekly mood trends, wellbeing scores, and journal history

## Stack

| Layer | Tech |
|---|---|
| Backend API | Python / Flask |
| Emotion (text) | HuggingFace Transformers — `distilbert-base-uncased-emotion` |
| Emotion (audio) | HuggingFace — `Hatman/audio-emotion-detection` + OpenSMILE |
| Transcription | AssemblyAI API |
| LLM / chat | Google Gemini API (`google-genai`) |
| Activity detection | spaCy `en_core_web_sm` |
| Storage | CSV (flat file, `backend/data/journal_entries.csv`) |
| Frontend | Next.js 15, Tailwind CSS, shadcn/ui |

## Running locally

### 1. Backend

**Prerequisites:** Python 3.10+, pip

```bash
cd backend
pip install -r requirements.txt
python -m spacy download en_core_web_sm
```

Create a `.env` file inside `backend/`:

```
GOOGLE_API_KEY=your_gemini_api_key
```

> Get a Gemini API key at https://aistudio.google.com — free tier is sufficient.

Start the backend:

```bash
python app.py
```

Runs on `http://localhost:5000`.

**Note:** On first run, `transformers` will download two models (~500 MB total — DistilBERT and Hatman audio model). This is a one-time download; subsequent starts use the local cache.

### 2. Frontend

**Prerequisites:** Node.js 18+, npm

```bash
cd frontend
npm install
npm run dev
```

Runs on `http://localhost:3000`. The frontend expects the backend at `http://127.0.0.1:5000` (hardcoded — no env config needed for local dev).

## API routes

| Method | Route | Description |
|---|---|---|
| POST | `/save-journal-entry` | Submit text or audio journal entry |
| GET | `/api/journal-entries` | Fetch all journal entries |
| GET | `/api/wellbeing-score` | Most recent wellbeing score |
| GET | `/api/wellbeing-scores-last-7-days` | Wellbeing scores for last 7 entries |
| POST | `/analyze-text` | Emotion analysis on arbitrary text |
| POST | `/analyze-voice` | Emotion analysis on audio file |
| POST | `/api/chat` | Chat with Gemini using journal context |
| POST | `/generate-questions` | Generate follow-up questions from a log |
| POST | `/analyze-activity-impact` | Rank activities by wellbeing impact |

## Credentials & sensitive data

| Key | Required | Used for | Status |
|---|---|---|---|
| `GOOGLE_API_KEY` | Yes | Gemini (chat, question generation) | Set in `backend/.env` — never committed |
| AssemblyAI key | Yes | Audio transcription | Hardcoded in `utils/transcription.py` — **rotate before sharing** |

> **TODO before making repo fully public:** replace the hardcoded AssemblyAI key in `utils/transcription.py` with `os.getenv("ASSEMBLYAI_API_KEY")` and add it to `.env`.

## Notes

- Journal entries persist in `backend/data/journal_entries.csv`. Delete this file to reset all data.
- Garmin Connect integration has been removed — the API credentials are short-lived and require an active Garmin device.
- The Flask template frontend (`frontend/app.py` + `frontend/templates/`) is an older prototype. Use the Next.js frontend for the full experience.
