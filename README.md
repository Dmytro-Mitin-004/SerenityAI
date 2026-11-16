# SerenityAI

A mental wellness platform built for the Innovaite hackathon. Combines voice and text journalling with wearable data (Garmin) to track emotional wellbeing over time — using on-device NLP for emotion classification and Google Gemini for personalised feedback.

## What it does

- **Text journalling** — submit journal entries; the backend classifies emotion (DistilBERT), extracts activities (spaCy NER), and scores wellbeing
- **Voice journalling** — record audio; the backend transcribes it (Whisper), runs tone analysis (openSMILE), and classifies emotion
- **Wellbeing dashboard** — 7-day wellbeing trend, mood distribution, Gemini-generated insights, activity-impact rankings
- **Garmin integration** — pulls sleep, heart rate, activity, and stress data from a Garmin Connect account; Gemini generates a personalised health summary
- **AI chat** — GPT-Neo 1.3B answers questions about your journal history

## Stack

| | |
|---|---|
| Backend | Python 3 / Flask, flask-cors |
| NLP / ML | HuggingFace Transformers (DistilBERT, GPT-Neo 1.3B), spaCy, openSMILE |
| Transcription | OpenAI Whisper (via `transformers`) |
| Wearables | Garmin Connect API (`garminconnect`) |
| AI | Google Gemini (`google-genai`) |
| Frontend | Next.js, Tailwind CSS, shadcn/ui |
| Data store | CSV (journal entries) |

## Running the backend

**Requirements:** Python 3.10+, ~8 GB free disk space (GPT-Neo 1.3B + ML models auto-download on first run)

```bash
git clone https://github.com/Trexicurity07/SerenityAI.git
cd SerenityAI/backend
pip install -r requirements.txt
python -m spacy download en_core_web_sm
```

### Credentials (required for full functionality)

The app starts without credentials but Garmin and Gemini endpoints will return errors until configured. Set these as environment variables before running:

```bash
# Google Gemini API key — get one free at https://aistudio.google.com/
export GOOGLE_API_KEY="your-gemini-api-key"

# Garmin Connect login (your personal Garmin account)
export GARMIN_USERNAME="your-garmin-email@example.com"
export GARMIN_PASSWORD="your-garmin-password"
```

On Windows (PowerShell):
```powershell
$env:GOOGLE_API_KEY = "your-gemini-api-key"
$env:GARMIN_USERNAME = "your-garmin-email@example.com"
$env:GARMIN_PASSWORD = "your-garmin-password"
```

Then start the server:

```bash
python app.py
# Runs on http://127.0.0.1:5000
```

> The first run downloads GPT-Neo 1.3B (~5 GB) automatically via HuggingFace. Subsequent starts are fast.

### Without credentials

Journal entries, emotion analysis, and the wellbeing dashboard all work without any credentials. Only the `/analyze-Garmin` and Gemini-powered routes need the keys above.

## Running the frontend

**Requirements:** Node.js 18+

```bash
cd SerenityAI/frontend
npm install
npm run dev
# Runs on http://localhost:3000
```

The frontend expects the backend at `http://127.0.0.1:5000`.

## API overview

| Method | Endpoint | Description |
|---|---|---|
| POST | `/save-journal-entry` | Save text entry — classifies emotion and scores wellbeing |
| POST | `/analyze-text` | Classify emotion from text |
| POST | `/analyze-voice` | Transcribe audio and classify emotion |
| POST | `/generate-questions` | Generate reflective journal prompts |
| GET | `/api/wellbeing-score` | Current overall wellbeing score |
| GET | `/api/wellbeing-scores-last-7-days` | 7-day trend |
| GET | `/api/journal-entries` | All saved entries |
| POST | `/api/chat` | Chat with GPT-Neo about your journal |
| POST | `/analyze-Garmin` | Fetch Garmin data + Gemini summary (requires credentials) |
| POST | `/analyze-activity-impact` | Rank activities by their effect on wellbeing |
