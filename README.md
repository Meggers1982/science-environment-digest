# Science & Environment Research Digest

A daily PubMed monitoring tool that surfaces new research in environmental health, public health, genetics, infectious disease, and pharmacology — with AI-generated story pitch angles for science and environment journalists.

## What it does

Each day, a GitHub Actions workflow:
1. Queries PubMed for recent studies across five science/environment journal categories
2. Filters out editorials, letters, and widely-covered stories (via SERPAPI)
3. Sends studies through Claude with a science journalism prompt that produces structured pitch angles
4. Merges results into a GitHub Pages dashboard

## Categories

- **Environmental Health** (97 journals) — air quality, PFAS, microplastics, climate and health, chemical exposure
- **Public Health & Epidemiology** (148 journals) — disease burden, surveillance, pandemic preparedness, health equity
- **Genetics & Molecular Biology** (273 journals) — GWAS, CRISPR, gene expression, molecular mechanisms
- **Infectious Disease** (229 journals) — pathogen biology, antibiotic resistance, outbreak dynamics, vaccines
- **Pharmacology & Drug Therapy** (174 journals) — drug trials, mechanisms of action, adverse effects, drug access

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
| `SERPAPI_KEY` | SerpAPI key (optional — skips media filter if absent) |
| `SUPABASE_URL` | Supabase project URL (enables personalization from dashboard save/delete feedback) |
| `SUPABASE_KEY` | Supabase API key (read-only use; optional) |
| `DASHBOARD_REPO_TOKEN` | Token with push access to the shared `research-digest-dashboard` repo |

### GitHub Pages

Enable GitHub Pages in repo Settings → Pages → Deploy from branch → `main` / `root`.

## Local development

```bash
pip install -r requirements.txt

# Run the digest locally (requires env vars)
export ANTHROPIC_API_KEY=...
CATEGORIES="Environmental Health" python scripts/science_environment_digest.py
```

## Data files

- `data/*.csv` — journal lists per category. Originally extracted from `PubMed_Journals_Categorized.xlsx` by `scripts/extract_journals.py`; that workbook no longer exists, so the CSVs are now hand-maintained and are the source of truth. Don't re-run `extract_journals.py` — it would overwrite hand-added rows.
- `data/results.json` — accumulated study results powering the dashboard

## Journal list audit (2026-09-14)

Method: pulled OpenAlex's top sources for this digest's subject areas over the prior year, diffed them against the CSVs by ISSN and title, and kept only titles NCBI lists with PubMed articles in the last 12 months. Because the digest pulls every article a listed journal publishes (no topic filter), a journal had to sit entirely inside the human-health science beat to be added.

Added (12):

| Journal | ISSN | CSV | PubMed/yr |
|---|---|---|---|
| Vaccine | 1873-2518 | Infectious Disease | ~1,230 |
| Open Forum Infectious Diseases | 2328-8957 | Infectious Disease | ~805 |
| npj Vaccines | 2059-0105 | Infectious Disease | ~275 |
| One Health | 2352-7714 | Infectious Disease | ~445 |
| Journal of Medical Entomology | 1938-2928 | Infectious Disease | ~230 |
| Journal of Viral Hepatitis | 1365-2893 | Infectious Disease | ~150 |
| International Journal of Tuberculosis and Lung Disease | 1815-7920 | Infectious Disease | ~115 |
| npj Viruses | 2948-1767 | Infectious Disease | ~65 |
| Environment & Health (ACS) | 2833-8278 | Environmental Health | ~210 |
| GeoHealth | 2471-1403 | Environmental Health | ~140 |
| The Journal of Climate Change and Health | 2667-2782 | Environmental Health | ~135 |
| Environmental Epidemiology | 2474-7882 | Environmental Health | ~110 |

Notable exclusions:

- **Mega-journals / volume:** PLOS Global Public Health (~1,140/yr) and MDPI's *Vaccines* (~1,000/yr) would crowd the 30 candidate slots per job. *Journal of Fungi* and *Tropical Medicine and Infectious Disease* (both MDPI) and *Infection and Drug Resistance* (Dove, mostly single-center regional studies) were also left out.
- **In the beat but effectively not in PubMed:** *Air Quality, Atmosphere & Health*, *Exposure and Health*, *Environmental Research: Health* and *Urban Forestry & Urban Greening* publish plenty in this space but have fewer than 20 PubMed records a year. *Leprosy Review* isn't in NCBI's journal list at all.
- **Mixed or off-beat scope:** *Human Vaccines & Immunotherapeutics* (half cancer immunotherapy), *Autophagy* (cell-biology mechanism work), *Forensic Science International: Genetics* (forensic DNA methods), *Genetics Selection Evolution* (livestock breeding), *Journal of Cannabis Research* (includes cultivation and plant chemistry), plus the addiction, palliative care, neuro-oncology, hepatology/IBD, pulmonology and medical-education titles OpenAlex's classifier lumped in. Several of those are covered by sibling digests.
- **Narrow or regional:** *Transplant Infectious Disease* and *Infection, Disease & Health* (niche clinical/infection-control practice), *Public Health Action* and *IJTLD Open* (mostly single-country program reports the prompt's single-region rule would exclude anyway), *Tuberculosis* (mainly animal-model TB biology).
- **Case reports / non-English:** *Medical Mycology Case Reports*, *Revista Clínica Española*.
