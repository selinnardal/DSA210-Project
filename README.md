# DSA 210 — Spotify Listening Project

**Selin Nardal** · Spring 2026

This repository contains my term project for **DSA 210 — Introduction to Data Science** at Sabancı University.  The project analyzes my personal Spotify listening history (October 2023 – March 2026, 15,581 plays across 5,453 unique tracks) using exploratory analysis, hypothesis testing, and machine learning.

---

## Research questions and hypotheses

The proposal poses three research questions and two main hypotheses, all rooted in my own listening behavior.

| ID | Question / hypothesis |
|----|-----------------------|
| **H1** | Late-night listening differs from daytime listening in measurable behavioral terms (duration, skip rate, manual-skip rate). |
| **H2** | Skip behavior is predictable from features available at the start of a play (time-of-day, weekday, platform, etc.). |
| **RQ2** | Does my listening behavior change measurably during exam periods? |
| **RQ3** | What latent profiles exist among my tracks and artists? |

H1 was originally framed in terms of Spotify's `valence` audio feature.  Spotify deprecated the public `audio-features` endpoint in November 2024, so H1 is now operationalized through **behavioral proxies** — see `04_lastfm_enrichment.ipynb` and the methodological note at the top of `05_ml_models.ipynb`.

---

## Repository structure

```
.
├── 01_data_collection.ipynb     Load + clean Spotify Extended History JSON
├── 02_eda.ipynb                 Exploratory data analysis (12 figures)
├── 03_hypothesis_tests.ipynb    Statistical tests for H1, H2, RQ2
├── 04_lastfm_enrichment.ipynb   Last.fm tag fetching (mood proxy attempt)
├── 05_ml_models.ipynb           Four ML models + behavioral H1 re-test
├── data/
│   ├── Streaming_History_Audio_*.json   (raw, gitignored)
│   ├── streaming_clean.csv              (cleaned, feature-engineered)
│   └── lastfm_tags/                     (cached Last.fm responses)
├── figures/                     All saved plots (eda + ml prefixes)
├── requirements.txt
└── DSA210_Proposal_Spotify (1).docx
```

---

## How to reproduce

### 1. Clone and set up the environment

```bash
git clone https://github.com/selinnardal/DSA210-Project.git
cd DSA210-Project
pip install -r requirements.txt
```

### 2. Provide API credentials (only needed for notebook 04)

Create a `.env` file in the project root:

```
LASTFM_API_KEY=your_lastfm_key_here
SPOTIPY_CLIENT_ID=your_spotify_client_id
SPOTIPY_CLIENT_SECRET=your_spotify_client_secret
```

You can obtain a free Last.fm API key at https://www.last.fm/api/account/create.  Spotify credentials are at https://developer.spotify.com/dashboard (note: Spotify deprecated the public `audio-features` endpoint in Nov 2024, so the Spotify keys are kept for historical compatibility only — actual enrichment uses Last.fm).  The `.env` file is gitignored so keys never enter version control.

### 3. Run the notebooks in order

```bash
jupyter lab
```

Open and run:

1. `01_data_collection.ipynb` — produces `data/streaming_clean.csv`
2. `02_eda.ipynb` — produces `figures/01_*` through `figures/09_*`
3. `03_hypothesis_tests.ipynb` — produces `figures/h1_*`, `figures/h2_*`, `figures/rq2_*`
4. `04_lastfm_enrichment.ipynb` — produces `data/lastfm_tags/all_tags.json` (full run takes ~25 min)
5. `05_ml_models.ipynb` — produces `figures/ml_01_*` through `figures/ml_07_*`

The data file (`streaming_clean.csv`) is committed, so notebooks 02, 03, and 05 can be run without re-running notebook 01.

---

## Models and key findings

Notebook 5 implements four models, each tied to one of the research questions.

| # | Model | Question | Key result |
|---|-------|----------|------------|
| 1 | K-Means clustering on tracks | RQ3 — latent track profiles | k = 2 (silhouette 0.22); two distinct listening profiles emerge |
| 2 | Random Forest — skip prediction | H2 — predictability of skips | ROC-AUC = 0.750, accuracy 0.73 |
| 3 | Logistic Regression — exam vs. non-exam | RQ2 — exam-period effect | ROC-AUC = 0.64; signed coefficients show direction of effect |
| 4 | Hierarchical clustering on artists | RQ3 — artist groupings | 4 clusters separating Turkish pop, Western pop, dance/party, and ambient/classical artists |

The behavioral re-test of **H1** (in notebook 05) finds:

- Listening duration differs significantly between late-night and daytime (Mann-Whitney *p* < 0.0001)
- Skip rate differs significantly (chi-square *p* < 0.0001)
- Forward-skip rate does not (chi-square *p* = 0.30)

So H1 is supported by the duration and overall-skip evidence, but not by the manual-skip-rate sub-test.

---

## Final report

The complete final report (Motivation, Data Source, Data Analysis, Findings, Limitations) is available in two formats:

- **[`REPORT.md`](REPORT.md)** — full markdown version with embedded figures, viewable directly on GitHub
- **[`REPORT.pdf`](REPORT.pdf)** — printable PDF version

---

## AI usage disclosure

In line with the course's AI policy, I used a large language model (Anthropic's Claude) as a coding and writing assistant during this project.  Areas where I used AI assistance include:

- Debugging help for the Last.fm API integration after the Spotify `audio-features` endpoint was deprecated.
- Sanity-checking the choice of behavioral proxies for H1 once mood-tag coverage proved limited (~22% hit rate).
- Drafting and refining markdown explanations in the notebooks and this README.
- Discussing how to frame the methodological adaptation (valence → behavioral proxies) for the final report.

All hypotheses, the choice of models and features, the final interpretations, and the substantive analysis decisions are mine; the assistant was used for technical scaffolding and as a sounding board.

A detailed log of specific prompts and outputs is provided in [`AI_USAGE.md`](AI_USAGE.md) as required by the course AI policy.

---

## Author

**Selin Nardal**
DSA 210 — Spring 2026
Sabancı University
