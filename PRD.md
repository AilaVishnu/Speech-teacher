# Product Requirements Document — Off The Cuff (Speak Trainer)

**Status:** Draft v1.0
**Last updated:** 2026-06-06
**Owner:** dexterpittampally@gmail.com

---

## 1. Overview

**Off The Cuff** is a web app that helps people overcome the fear of public speaking by giving them a daily speaking prompt, recording their off-the-cuff response, and returning **instant, objective feedback** on their delivery — all running **free and privately in the browser**, with no accounts and no paid services.

> Public speaking is the #1 fear in the world — ranked above heights, spiders, and death. Almost no one teaches us how to get past it. The only cure is reps. This app makes reps frictionless, private, and measurable.

---

## 2. Problem

- Most people never practice speaking because it feels awkward, requires an audience, and gives no feedback.
- Existing coaching is expensive, scheduled, and intimidating.
- People can't see their own bad habits (filler words, rushing, rambling) without an outside observer.

**Insight:** The barrier isn't knowledge — it's *low-friction, judgment-free repetition with a feedback loop.*

---

## 3. Goals & Non-Goals

### Goals
- Let a user complete a full practice rep (prompt → speak → feedback) in **under 60 seconds**, with zero setup.
- Give **objective, repeatable metrics** the user can improve over time.
- Run at **$0 cost**, fully in-browser, with **no data leaving the device**.
- Build a **habit loop** (streaks) that brings users back daily.

### Non-Goals (for now)
- Human/live coaching.
- Social features / sharing / leaderboards.
- Mobile native apps (web is responsive and sufficient).
- Multi-language support (English-first).

---

## 4. Target Users

| Persona | Need |
|--------|------|
| **The Nervous Professional** | Practice before meetings/presentations privately, without embarrassment. |
| **The Student / Interviewee** | Rehearse answers, reduce "um"s, control pacing. |
| **The Aspiring Speaker / Creator** | Build a daily speaking habit and track measurable progress. |

Common thread: wants to improve **alone, privately, and for free.**

---

## 5. Core User Flow

1. User opens the app → sees a **speaking prompt** ("Describe the best meal you've ever eaten").
2. (Optional) Taps **↻ New prompt** for a different topic.
3. Taps **Record**, speaks off the cuff for ~30–90 seconds.
4. Taps **Stop**.
5. App transcribes locally and shows:
   - Full transcript with **filler words highlighted**
   - **Words per minute** (with pace guidance)
   - **Filler-word count** + per-word breakdown
   - **Total words**
   - **Audio playback** to hear themselves
6. **Streak** increments. User repeats.

---

## 6. Features

### 6.1 Shipped (MVP — v1)
- [x] Daily speaking prompt with shuffle (15 built-in prompts)
- [x] Browser audio recording (MediaRecorder) + playback
- [x] **Local speech-to-text** via Whisper (`whisper-tiny.en`, transformers.js) — runs on-device, works offline after first load, works in any browser incl. Opera
- [x] Filler-word detection + highlighting (um, uh, like, you know, basically, …)
- [x] Words-per-minute with pace rating (slow / great / too fast)
- [x] Filler-word breakdown chips
- [x] Practice streak (localStorage)
- [x] Fully private — no uploads, no account, no backend

### 6.2 Next (v1.1 — quick wins)
- [ ] **Session history** — list of past attempts (date, prompt, scores) in localStorage
- [ ] **Progress chart** — WPM and filler-rate trend over time
- [ ] **Long-pause / hesitation detection** (silence gaps in audio)
- [ ] **Repeated-word detection** ("the the", "and and")
- [ ] **Timed challenge mode** — "Speak for exactly 60 seconds"
- [ ] **Better prompt sets** — categories (interview, storytelling, persuasion, impromptu)

### 6.3 Later (v2 — depth)
- [ ] **Multi-step challenge flow** ("Continue" progression like a daily course)
- [ ] **Smart AI coaching** — qualitative feedback ("strong opening, weak close") via:
  - Free path: self-hosted LLM (Ollama + Llama/Mistral), **or**
  - Easy path: cheap API (Anthropic/OpenAI), opt-in, user-supplied key
- [ ] **Vocal variety / energy** signals (volume dynamics, pitch range)
- [ ] **Goal setting** ("get filler rate under 2%")
- [ ] **Export** transcript + report (PDF/text)

---

## 7. Technical Architecture

**Principle:** Maximize what runs free, in-browser, with no servers.

| Layer | Choice | Cost |
|-------|--------|------|
| App shell | Single static HTML/CSS/JS file | $0 |
| Audio capture | MediaRecorder API (browser native) | $0 |
| Speech-to-text | Whisper `tiny.en` via transformers.js (WASM/WebGPU, on-device) | $0 |
| Analysis | Plain JavaScript (filler/pace/word logic) | $0 |
| Storage | Browser `localStorage` (streaks, future history) | $0 |
| Local dev server | `python -m http.server` (needed so the model/CDN loads correctly) | $0 |
| Hosting (future) | GitHub Pages / Netlify / Vercel (static, free tier) | $0 |

**Notes / constraints:**
- Whisper model (~40 MB) downloads once from CDN, then is cached in the browser for offline use.
- Transcription runs on the user's CPU — a few seconds of latency per recording (acceptable tradeoff for free + private).
- Must be served over `http://localhost` or `https://` (microphone + module loading require a secure context; `file://` is unreliable).

---

## 8. Non-Functional Requirements

- **Privacy:** No audio or transcript ever leaves the device. State this clearly in the UI.
- **Cost:** $0 to run and host indefinitely at small scale.
- **Performance:** Feedback returned within ~5s of stopping (tiny model on typical laptop).
- **Compatibility:** Works in Chromium browsers (Chrome, Edge, **Opera**), modern Firefox, Safari (WASM path).
- **Accessibility:** Keyboard-operable controls; sufficient color contrast.
- **Offline:** Fully functional after first model download.

---

## 9. Success Metrics

| Metric | Target |
|--------|--------|
| Time to first feedback (new user) | < 90 seconds (incl. model load) |
| Reps per active user per session | ≥ 3 |
| Day-2 / Day-7 return (habit) | Track via streak retention |
| Filler-rate improvement over 7 sessions | Measurable downward trend |
| Cost per user | $0 |

---

## 10. Risks & Mitigations

| Risk | Mitigation |
|------|-----------|
| 40 MB model download feels slow on first visit | Progress bar + clear "one-time, then offline" messaging; consider even smaller model |
| On-device transcription too slow on weak hardware | Use `tiny.en`; offer quality/speed toggle later (`base.en`) |
| Whisper transcription accuracy on accents/noise | Set expectations; allow manual transcript edit before scoring (future) |
| Filler/pace heuristics feel arbitrary | Tune thresholds; cite ranges (e.g., 110–160 WPM is conversational) |
| `file://` breaks mic/model | Always serve via localhost/https; document it |

---

## 11. Milestones

1. **M1 — MVP (done):** prompt → record → local transcribe → filler/pace/words → streak.
2. **M2 — Progress:** session history + trend chart + hesitation/repeat detection.
3. **M3 — Challenge modes:** timed mode, prompt categories, multi-step daily flow.
4. **M4 — AI coaching:** opt-in qualitative feedback (self-hosted or API).
5. **M5 — Public launch:** deploy to free static host, polish onboarding.

---

## 12. Open Questions

- Should AI coaching default to free self-hosted (harder setup) or optional paid API (easier, costs pennies)?
- Daily-prompt cadence: truly one fixed prompt per day, or free practice anytime?
- Do we ever want optional cloud sync (accounts) for cross-device history, or stay 100% local?
