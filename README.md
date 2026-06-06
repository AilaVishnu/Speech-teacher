# Off The Cuff — Speak Trainer

A free, private, in-browser public-speaking trainer. Get a prompt, speak off the cuff, and get instant feedback on your delivery — filler words, pace, and word count — with **all speech transcription running locally on your device** (no uploads, no accounts, no paid services).

## Features

- 🎯 **Daily speaking prompts** (with shuffle)
- 🎙️ **Record + playback** your response (browser `MediaRecorder`)
- 🧠 **On-device transcription** via Whisper (`whisper-tiny.en`, transformers.js) — works offline after first load, in any browser including Opera
- 🚫 **Filler-word detection** ("um", "uh", "like", "you know", …) with highlighting
- ⏱️ **Words-per-minute** with pace guidance (110–160 = conversational)
- 📊 **Progress charts + session history** (saved locally)
- 🔥 **Practice streak** to build the habit

## Run it locally

Speech transcription requires the page to be served over `http://localhost` (not opened as a `file://` path). From this folder:

```bash
python -m http.server 8000
```

Then open **http://localhost:8000** in your browser.

> First run downloads the Whisper model (~40 MB) once, then caches it for offline use. Allow microphone access when prompted.

## Tech

Single static `index.html` — no build step, no backend. Everything runs client-side:

| Concern | Tool | Cost |
|--------|------|------|
| Audio capture | MediaRecorder API | $0 |
| Speech-to-text | Whisper via transformers.js (WASM) | $0 |
| Analysis | Plain JavaScript | $0 |
| Storage | Browser `localStorage` | $0 |

See [PRD.md](PRD.md) for the full product spec and roadmap.
