# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

**Vidente Interview Scoresheet** — a client-side web app for structured interview assessment of finance/CFO-level candidates. Nine HTML scoresheets (one per candidate) with real-time scoring, localStorage persistence, and split-view PDF resume review. All UI text is in Korean.

- No build system, no npm, no framework — pure HTML/CSS/vanilla JS
- Deployed as static files via GitHub Pages: `https://github.com/kimmove-blip/interview-scoresheet.git`

## Running Locally

```bash
# Recommended (required for PDF iframe to load correctly)
cd interview-deploy
python3 -m http.server 8000
# Visit: http://localhost:8000/김남경.html

# Or open directly in browser (PDF may fail due to CORS)
open 김남경.html
```

## File Structure

Each candidate has a dedicated HTML file (e.g., `김남경.html`, `강솔기.html`). All logic, styles, and markup are **self-contained in a single file per candidate** — there are no external JS/CSS imports.

Password for all PDF resumes: `vidente2025`

## Architecture

### Scoring System
- 18 questions total across 6 sections, each scored 1–5
- Section weights sum to 100 points: sec1(25) + sec2(20) + sec3(15) + sec4(15) + sec5(15) + sec6(10)
- `sectionMap` object maps question keys (`q1_1`…`q6_2`) to section IDs
- `updateScores()` recalculates totals and updates section badge displays after every score change

### Persistence
- All state (scores, notes, metadata, final result) serialized to `localStorage`
- Storage key: `interview_[candidate_key]` — each file has a unique hardcoded key
- **No auto-save** (removed Oct 2026 to prevent overwriting existing data on load) — save is manual only

### PDF Split-View
- Right-panel iframe loads the candidate's resume PDF
- Password auth uses `sessionStorage` to avoid repeated prompts within a tab
- `togglePdf()` / `openPdf()` / `showPdfAuth()` / `confirmPdfPassword()` handle the flow

### When Adding a New Candidate
1. Copy an existing candidate HTML file
2. Change the `candidateKey` constant (used for localStorage key)
3. Update the `pdfFile` constant to point to their resume PDF
4. Update candidate-specific questions in the scoring sections
5. Add a nav link in the header `<nav>` block
6. Add the new PDF to the repo

### Key JS Functions

| Function | Purpose |
|---|---|
| `setScore(btn, key)` | Updates `scores` object and calls `updateScores()` |
| `updateScores()` | Recalculates section/total scores, updates badge UI |
| `setResult(r)` | Sets pass/hold/fail state and highlights result button |
| `saveData()` | Serializes all state to localStorage |
| `loadData()` | Restores state from localStorage, re-renders UI |
| `resetAll()` | Clears all inputs and localStorage entry |
