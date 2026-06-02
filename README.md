# Gujarat ACPC Cutoff — Static Site

Static site to predict Gujarat engineering college admission based on ACPC rank.

## Stack
- Pure static HTML + Tailwind (CDN) + vanilla JS modules
- Data parsed once from official ACPC PDFs → `site/data/data.json`
- No backend, no build step, no tracking

## Local dev
```sh
cd site && python3 -m http.server 8080
# open http://localhost:8080
```

## Re-parse PDFs
```sh
python3 -m venv .venv && .venv/bin/pip install pdfplumber
# drop new PDFs into data/raw/{branch,institute}.pdf
.venv/bin/python scripts/parse.py
cp data/data.json site/data/data.json
```

## Deploy
- Netlify: connect repo, publish dir = `site/`, no build command.
- Or drag `site/` folder to https://app.netlify.com/drop.

## Features
- 🎯 Rank predictor with 🟢 Likely / 🟡 Maybe / 🔴 Reach buckets
- 🏛️ Browse by college
- 📚 Browse by branch
- ⚖️ Compare up to 4 colleges side-by-side (GEN)
- ⭐ Shortlist saved in localStorage
- 📱 WhatsApp share + 🖨️ print
- 🔗 Shareable result URLs (`#predict?rank=15000&category=GEN`)

## Data
- Source: Mock Round A.Y. 2024-25 — Branch Wise + Institute Wise Closing Rank PDFs
- Rows: ~2,400 cutoffs · ~137 colleges · ~150 branches
