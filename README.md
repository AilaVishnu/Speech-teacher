# SpeechTeacher

A free, private public-speaking trainer and marketing site. Get a prompt, speak off the cuff, and get instant feedback on your delivery — filler words, pace, and word count — with **all speech transcription running locally on your device** (no uploads, no accounts, no paid services).

Production domain: **speechteacher.in**

## Stack

- **[Astro](https://astro.build)** — multi-page (MPA) architecture for SEO; every route is static HTML
- **[Tailwind CSS v4](https://tailwindcss.com)** — CSS-first design tokens (Vercel design language, see [DESIGN.md](DESIGN.md))
- **[transformers.js](https://github.com/xenova/transformers.js)** — on-device Whisper transcription (WASM), no backend

## Pages

| Route | Purpose |
|-------|---------|
| `/` | Landing — hero, how-it-works, features, comparison |
| `/features` | Feature deep-dives |
| `/how-it-works` | The practice loop + FAQ |
| `/pricing` | Free + Pro tiers |
| `/about` | Mission and principles |
| `/practice` | The on-device speech trainer |
| `/resources` | SEO articles |

## Develop

```bash
npm install
npm run dev      # http://localhost:4321
npm run build    # static output in dist/
npm run preview  # preview the production build
```

> The trainer downloads the Whisper model (~40 MB) once on first use, then caches it for offline use. Allow microphone access when prompted.

## Project docs

- [PRD.md](PRD.md) — product spec and roadmap
- [DESIGN.md](DESIGN.md) — Vercel-inspired design system reference
