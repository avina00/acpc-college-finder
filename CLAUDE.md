# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

Static web tool for Gujarat ACPC engineering college cutoff data. Students enter rank + category → see which colleges/branches they can get. Data comes from official ACPC PDFs parsed into JSON.

## Commands

### Parse PDFs → JSON
```bash
source .venv/bin/activate
python scripts/parse.py
# Writes data/data.json (2400+ rows)
```
After parsing, manually copy to site:
```bash
cp data/data.json site/data/data.json
```

### Inspect PDF structure (debug)
```bash
source .venv/bin/activate
python scripts/inspect_pdf.py data/raw/branch.pdf
python scripts/inspect_pdf.py data/raw/institute.pdf
```

### Serve the frontend
```bash
cd site && python3 -m http.server 8080
# Open http://localhost:8080
```
No build step. `site/js/app.js` loads as an ES module directly.

## Architecture

**Data pipeline** (one-time, manual):
```
data/raw/branch.pdf  ─┐
                       ├─► scripts/parse.py ─► data/data.json ─► site/data/data.json
data/raw/institute.pdf ┘
```
Both PDFs contain the same rows in different order. `parse.py` dedupes via a tuple key across all 7 fields. Output JSON shape:
```json
{ "meta": {...}, "institutes": [...], "branches": [...], "categories": [...], "types": [...], "rows": [...] }
```

**Frontend** (`site/`):
- Single `index.html` + `js/app.js` — no framework, no bundler, no npm
- Tailwind CSS loaded from CDN
- `app.js` fetches `data/data.json` at runtime and holds all state in a single `STATE` object
- Shortlist persisted in `localStorage` under key `acpc.shortlist`
- Tab routing via `location.hash`; shareable URLs encode rank/category/type/branch as query string after `#predict?`

**Bucket thresholds** (in `app.js:bucket()`):
- `safe`: `last_rank - userRank >= 2000`
- `mid`: `>= -1000`
- `reach`: `>= -5000`
- Beyond reach: not shown

**Compare tab** only shows GEN category closing ranks. Other categories require Predict tab.

## Data schema

Each row in `data.json`:
```
institute, branch, category, quota, type, first_rank, last_rank
```
`type` values: `"Govt/GIA"` or `"Self-Finance"`.
Category values include: `GEN`, `EWS`, `SEBC`, `SC`, `ST`, `GEN-PH`.

## Dependencies

Python: `pdfplumber` (installed in `.venv`). No other deps.
Frontend: zero npm dependencies; Tailwind via CDN only.
