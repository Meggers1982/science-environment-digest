# Science & Environment Research Digest

A daily PubMed monitoring tool that surfaces new research in environmental health, public health, genetics, infectious disease, and pharmacology — with AI-generated story pitch angles for science and environment journalists.

## What it does

Each day, a GitHub Actions workflow:
1. Queries PubMed for recent studies across five science/environment journal categories
2. Filters out editorials, letters, and widely-covered stories (via SERPAPI)
3. Sends studies through Claude with a science journalism prompt that produces structured pitch angles
4. Merges results into a GitHub Pages dashboard

## Categories

- **Environmental Health** — air quality, PFAS, microplastics, climate and health, chemical exposure
- **Public Health & Epidemiology** — disease burden, surveillance, pandemic preparedness, health equity
- **Genetics & Molecular Biology** — GWAS, CRISPR, gene expression, molecular mechanisms
- **Infectious Disease** — pathogen biology, antibiotic resistance, outbreak dynamics, vaccines
- **Pharmacology & Drug Therapy** — drug trials, mechanisms of action, adverse effects, drug access

## Pitch angles

Unlike the mental health digest (which generates a single New Scientist Mind pitch), this tool generates **1–3 pitch angles per study**, each framed for a different publication type:

- National Geographic — big-picture environmental or biological significance
- Scientific American — mechanistic detail, how-science-works angle
- The Atlantic — policy, equity, or societal implications
- Wired — technology, innovation, or systems angle
- General science — broad audience interest

## Dashboard

The GitHub Pages dashboard at the repo URL lets you:
- Filter by category, groundbreaking type, relevance score, date range
- Sort by relevance score or date
- Mark studies as New / Saved / Pitched / Passed
- Expand per-publication pitch angles
- Search across headlines, summaries, and pitch text

## Setup

### Secrets required

| Secret | Description |
|--------|-------------|
| `ANTHROPIC_API_KEY` | Anthropic API key |
| `RESEND_API_KEY` | Resend email API key |
| `SERPAPI_KEY` | SerpAPI key (optional — skips media filter if absent) |

### Variable required

| Variable | Description |
|----------|-------------|
| `DASHBOARD_URL` | GitHub Pages URL for the dashboard (used in email links) |

### GitHub Pages

Enable GitHub Pages in repo Settings → Pages → Deploy from branch → `main` / `root`.

## Local development

```bash
pip install -r requirements.txt

# Extract journals from spreadsheet
python scripts/extract_journals.py

# Run the digest locally (requires env vars)
export ANTHROPIC_API_KEY=...
export RESEND_API_KEY=...
CATEGORIES="Environmental Health" python scripts/science_environment_digest.py
```

## Data files

- `data/*.csv` — journal lists per category, extracted from PubMed journal spreadsheet
- `data/results.json` — accumulated study results powering the dashboard
