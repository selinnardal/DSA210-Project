# AI Usage Log — DSA 210 Project

**Author:** Selin Nardal
**Tool:** Anthropic's Claude (Claude.ai consumer interface)

This document fulfills the course's AI-policy requirement to disclose AI assistance in the project.

---

## 1. Scope of AI use

I used Claude as a coding and writing collaborator across several working sessions throughout the project. Across all sessions, the research questions, hypotheses, choice of models, interpretation of results, and final substantive decisions are mine. The assistant was used to scaffold code, debug specific errors, draft markdown explanations that I then edited, and act as a sounding board on methodological decisions.

---

## 2. Category A — Code scaffolding

The highest-volume use of AI assistance. I described what I wanted each notebook to accomplish at a high level, and the assistant produced notebook skeletons with section headings, library imports, and stub code that I then filled in, ran, and adjusted.

This included scaffolding for the data loading and cleaning pipeline (parsing Spotify timestamps, filtering podcasts/audiobooks, deriving time-of-day buckets), the EDA notebook structure, the hypothesis-testing setup, and the Last.fm API fetcher with rate-limiting and on-disk caching. I reviewed every line of scaffolded code before running it, adjusted feature engineering to match my analytical goals, and rejected suggestions when they did not fit (for example, derived ratios per artist that would have been too sparse to be stable).

---

## 3. Category B — Debugging

Smaller, targeted use. I described error messages or unexpected outputs and asked what was wrong. Examples included authentication issues with GitHub when a personal access token had expired, file-naming conflicts when re-downloading notebooks, and small Python errors during data cleaning. These were brief exchanges that resolved a specific blocker.

---

## 4. Category C — Methodological discussion

This is the use where the assistant's input shaped what the analysis actually says rather than just how the code is written.

**The audio-features deprecation problem.** When Spotify deprecated the public `audio-features` endpoint, the original H1 (framed around `valence`) became untestable. I discussed options with the assistant — drop H1, use Last.fm tags as a mood proxy, or find a third-party archive of audio features. The assistant laid out trade-offs (the AcousticBrainz archive had been discontinued in 2022; Last.fm tag coverage might be uneven for Turkish artists). I decided to keep H1 alive but reformulate it behaviorally (listening duration, skip rate) once Last.fm tag coverage on a 50-track sample turned out to be approximately 42%. The reformulation is documented in both the README and the report's Limitations section.

**Clustering result interpretation.** When K-Means returned a silhouette score of 0.22, I asked the assistant whether this was a good or weak result. It explained that 0.22 is modest — the clusters are real but the boundary is soft — and suggested I check k=3 and k=4 for comparison. Both produced smaller silhouettes and clusters dominated by single tracks, so I kept k=2 and described it honestly in the report.

**Outlier sensitivity analysis.** After identifying that the hierarchical-clustering solution had two small clusters of 2 and 5 artists, I asked the assistant how to formally check whether these were genuine clusters or outliers. It ran the silhouette analysis under three configurations (all artists at k=4, outliers removed at k=4, outliers removed at k=2) and reported the numbers. I kept the analysis and the explanatory paragraph in the final report (Section 4.2, "Outlier sensitivity check").

---

## 5. Category D — Drafting markdown explanations

I asked the assistant to draft section text for the notebooks and the final report, then edited and shortened the result. None of the section drafts went into the repository without me reading them through. The structure of the final report (Motivation → Data Source → Data Analysis → Findings → Limitations) follows the course guideline's bullet points. All numerical results in the Findings tables (play counts, U statistics, ROC-AUC values, etc.) were re-verified against my own notebook outputs before inclusion.

I shortened several paragraphs that drifted into essay mode, rejected phrasing that overclaimed the project's significance, and added personal observations where the assistant's draft was too neutral — for example, the references to recognizing the Western vs Turkish artist split in my own listening.

---

## 6. Category E — Clarification questions

Short prompts where I needed a definition or a refresher on a statistical concept (e.g., when Mann-Whitney U is preferred over a t-test, how to handle low expected counts in a chi-square test, how to interpret silhouette score ranges). These answers went into my own understanding, not into the repository.

---

## 7. What is *not* the assistant's work

To be specific, because the guideline asks for specificity:

- The **research questions and hypotheses** (H1, H2, RQ2, RQ3) are mine; they were written into the proposal before any AI-assisted work began.
- The **decision to use my own Spotify data** is mine.
- The **decision to reformulate H1 behaviorally** rather than drop it is mine, made after weighing the alternatives the assistant laid out.
- The **mapping of four ML models to four research questions** is mine.
- The **interpretation** of every finding is mine.
- The **limitations** I chose to surface — single-user external validity, the outlier-pre-cleaning gap, the approximate exam-window definition — are mine. They reflect what I think the real weaknesses of the work are.

The assistant is responsible for notebook scaffolding, the wording of many explanatory paragraphs, debugging help on specific errors, and acting as a sounding board on the methodological adaptation forced by the Spotify API deprecation. None of those is the same as deciding what to study or what the answers mean.
