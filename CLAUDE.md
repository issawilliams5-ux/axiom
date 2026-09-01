# Ponytail ruleset (always on)

Adapted for this repo from [DietrichGebert/ponytail](https://github.com/DietrichGebert/ponytail) — that repo is outside this session's GitHub access scope, so this is a manual port of the published ladder/philosophy, not the plugin itself. No `/ponytail lite|full|ultra|off` mode switching and no `/ponytail-review|-audit|-debt|-gain` commands here — those are plugin skill files this port doesn't include. If the real plugin ever becomes installable in your environment (`/plugin marketplace add DietrichGebert/ponytail` then `/plugin install ponytail@ponytail`), prefer that over this file.

The rule was never "fewest tokens." It is: **write only what the task needs, and never cut validation, error handling, security, or accessibility.** Code ends up small because it's necessary, not golfed.

## The ladder

Before writing code, stop at the first rung that holds:

1. Does this need to exist? → no: skip it (YAGNI)
2. Already in this codebase? → reuse it, don't rewrite
3. Stdlib does it? → use it
4. Native platform feature? → use it (e.g. reach for a native `<input>` before reaching for a component)
5. Installed dependency? → use it
6. One line? → one line
7. Only then: the minimum that works

Run the ladder *after* understanding the problem, not instead of it — read the code the change touches and trace the real flow before picking a rung. Lazy about the solution, never about reading.

## Never on the chopping block

Trust-boundary validation, data-loss handling, security, and accessibility are never cut for brevity, no matter what rung you land on.

# AXIOM — Equity Research Platform

## What this is
AXIOM is a BYOK (Bring Your Own Key) institutional-grade equity research generator. Users supply their own AI API key; AXIOM fetches real SEC EDGAR filings, runs a two-pass AI analysis, and outputs a full research note: DCF model, comps table, bull/bear thesis, risk matrix, investment recommendation.
Zero inference cost to the platform. No user data stored server-side. Keys stay in localStorage.

## Stack
- **Frontend:** React 18 + Vite (no framework router — single page)
- **Serverless API:** Vercel functions in `/api/` (same pattern as Dwelling)
- **Data:** SEC EDGAR public API (free, no key needed)
- **AI:** Provider-agnostic BYOK proxy — Cerebras, Groq, OpenRouter, Anthropic, OpenAI
- **Deployment:** Vercel

## File structure
```
axiom/
├── api/
│   ├── ai.js          # BYOK proxy — routes key to correct provider
│   └── edgar.js       # SEC EDGAR fetcher — ticker → CIK → financials
├── src/
│   ├── App.jsx
│   ├── main.jsx
│   ├── components/
│   │   ├── ApiKeyModal.jsx
│   │   └── ResearchReport.jsx
│   └── lib/
│       ├── ai.js      # Two-pass generateResearch()
│       ├── dcf.js     # DCF engine + fmt/pct helpers
│       └── storage.js # localStorage: key, history, provider detection
├── index.html
├── vite.config.js
├── vercel.json
└── package.json
```

## BYOK provider detection
- `csk-` → Cerebras → `llama-4-scout-17b-16e-instruct`
- `gsk_` → Groq → `llama-3.3-70b-versatile`
- `sk-or-` → OpenRouter → `meta-llama/llama-3.3-70b-instruct`
- `sk-ant-` → Anthropic → `claude-3-5-haiku-20241022`
- `sk-` → OpenAI → `gpt-4o-mini`

## Two-pass AI analysis
1. Pass 1 — Chain-of-thought: IB analyst reasons through the investment case
2. Pass 2 — Structured JSON extraction: recommendation, DCF assumptions, comps, risks, thesis

## Design language
- Background: `#0a0a0a`, Panels: `#111`
- Accent: `#38bdf8`, Positive: `#22c55e`, Negative: `#f87171`
- Fonts: IBM Plex Mono (labels/data), Inter (body)
- All styles inline React — no CSS framework

## Report sections
Header → Executive Summary → Investment Thesis → Financial Highlights → Bull/Bear → DCF Model → Comps Table → Key Risks → Analyst Note

## Git
Branch: `main`
Author: Dominique C <01dominique.c@gmail.com>
