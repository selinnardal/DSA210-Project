# AI Usage Log — DSA 210 Project

**Author:** Selin Nardal
**Tool:** Anthropic's Claude (Claude.ai consumer interface, used across multiple chat sessions between mid-April and mid-May 2026)

This document fulfills the course's AI-policy requirement to disclose AI assistance "including specific prompts used and outputs generated" (DSA 210 Spring 2026 Project Guidelines).

It is organized by **task category**, with representative prompts and a summary of what the assistant produced. Where I edited or rejected the assistant's output, I say so. Where I copied it more or less directly, I say that too.

---

## 1. Scope of AI use, in one paragraph

I used Claude as a coding and writing collaborator across roughly six longer working sessions: an initial project setup session (14 April), a Last.fm-API recovery session after Spotify deprecated the audio-features endpoint (30 April), an ML-modelling session on the milestone-2 deadline (5 May), a post-milestone ML-improvements session adding cross-validation, tuning and permutation importance (also 5 May), a token-renewal and check-in session (13 May), and a final-report-writing session (17 May, the day before final submission). Across all sessions, all hypotheses, all research questions, the choice of which models to apply to which question, the interpretation of every result, and the final wording I chose to keep are mine. The assistant was used to scaffold code, debug specific errors, draft markdown explanations that I then edited, and act as a sounding board on methodological decisions.

---

## 2. Category A — Code scaffolding for notebooks

The clearest, highest-volume use of AI assistance. I described what I wanted each notebook to do at a high level, and asked the assistant to produce a notebook skeleton I could fill in and run.

### Representative prompt (notebook 01 — data collection)

> *"biz seninle dsa için proje konusu seçmiştik umarım hatırlıyorsundur ve en son spotify'dan veri gelmesini bekliyorduk proje description'ını ekledim ekte hatta sonrasında proposal yazmıştık onu da ekledim şimdi ise vakit geldi projeye başlamamız lazım ve spotify verileri de geldi ne yapmam gerekiyor bilemedim hadi başlayalım"*

The assistant read the project guidelines and proposal I attached, then produced three notebook skeletons (`01_data_collection.ipynb`, `02_eda.ipynb`, `03_hypothesis_tests.ipynb`) with section headings, library imports, and stub code for loading the Spotify JSON, parsing timestamps, deriving time-of-day buckets, applying the 10-second minimum-play filter, and saving `streaming_clean.csv`. I ran the code, decided which sections to keep, and adjusted the feature engineering (e.g., I chose to keep `reason_start` and `reason_end` for later ML use; this was my call after seeing the columns in the cleaned CSV).

### Representative prompt (notebook 04 — Last.fm)

> *"spotify audio-features endpoint'i deprecated olmuş, alternatif olarak last.fm tag'lerini kullanabilir miyiz?"*

The assistant suggested Last.fm's `track.getTopTags` endpoint, explained the rate-limit etiquette (5 requests/second), and wrote a fetcher with on-disk caching to avoid re-hitting the API on every run. I added the `.env` loading pattern (after the assistant suggested it) and reviewed every line of the fetcher before running it against my real API key.

### What I rejected

The assistant initially proposed a more aggressive feature set for the ML notebook including derived ratios like `weekend_skip_rate` per artist. I dropped these because (a) many artists have too few weekend plays to make the ratio stable and (b) the model already had enough features. This is the kind of judgment call I made repeatedly when working with the assistant — taking the scaffolding, not the kitchen sink.

---

## 3. Category B — Debugging specific errors

Smaller, more targeted use. I pasted error messages or unexpected outputs and asked what was wrong.

### Representative prompt

> *"git push'ta authentication soruyor, token bitmiş galiba"*

The assistant walked me through generating a fresh personal access token on GitHub, setting an expiration date, and pasting it into the macOS keychain via `git credential-osxkeychain`. I followed the steps; the token issue was real (the previous token had hit its expiry) and the fix worked.

### Representative prompt

> *"05_ml_models.ipynb'yi push'a hazırlarken `05_ml_models (1).ipynb` diye bir dosya görüyorum, ne yapayım"*

The assistant identified this as a duplicate created when I re-downloaded the notebook from a chat session, told me to rename the canonical one back to `05_ml_models.ipynb` with `mv`, and continue with the commit. Straightforward, but it was a real moment of confusion that the assistant resolved in one turn.

---

## 4. Category C — Methodological discussion ("sounding board")

This is the use where the assistant's input mattered most, because it shaped what the analysis actually says rather than just how the code is written.

### The audio-features deprecation problem

**My prompt** (paraphrased from the 30 April session):

> *"Hipotezimizde tam olarak ne demiştik ve eğer ona uygunsa 1. ile ilerleyebiliriz ancak senin sezgilerine göre hocayı en tatmin edicek olanı yapalım"*

I had three options on the table after Spotify deprecated `audio-features`:
1. Drop H1 and reframe the project around behavioral hypotheses only.
2. Use Last.fm tags as a mood proxy.
3. Find a third-party archive of audio features.

**The assistant's response** laid out trade-offs:
- Option 1 was the most honest but also the most disruptive to a proposal I'd already submitted.
- Option 2 was viable but the tag coverage might be uneven, especially for Turkish-language tracks.
- Option 3 (AcousticBrainz) had been discontinued in 2022.

**What I decided.** I went with a combination — keep H1 alive but reformulate it behaviorally (listening duration, skip rate, forward-skip rate) once Last.fm tag coverage turned out to be ~42% on the sample I tested. The assistant suggested the duration+skip-rate framing; I agreed because it was directly testable on data I already had cleanly. The trade-off is explicitly written into both the README and the final report's Limitations section.

### The K-Means k = 2 result

**My prompt** (5 May, after seeing initial K-Means output):

> *"k=2 çıktı silhouette 0.22 — bu iyi mi kötü mü? hocayı tatmin eder mi?"*

**The assistant's response.** It explained that a silhouette of 0.22 is "modest, not strong" — the clusters are real but the boundary is soft — and that the honest move is to report this exactly that way rather than oversell the result. It also suggested I check what k=3 and k=4 look like for comparison. I tried both; both gave smaller silhouettes and clusters dominated by single tracks. I kept k=2 and described it honestly in the report.

### The cluster-outlier question

**My prompt** (17 May, after a peer-review-style critique pointed out that hierarchical-clustering clusters 2 and 3 had only 2 and 5 artists):

> *"Bunların aslında birer "küme" değil, "aykırı değer" (outlier) olduğu belirtilmiş ama bu durumun kümeleme kalitesini (Silhouette skorunu) nasıl etkilediği biraz daha deşilebilirdi"*

**The assistant's response.** It re-ran the silhouette analysis under three configurations (all artists at k=4, outliers removed at k=4, outliers removed at k=2), reported the numbers (0.203, 0.115, 0.202), and wrote a short paragraph explaining that the "natural" structure of the cleaned dataset is two clusters, and the four-cluster reporting is a presentational choice. I kept the analysis and the paragraph in the final report (Section 4.2, "Outlier sensitivity check") because it directly addressed the critique.

---

## 5. Category D — Drafting markdown explanations

I asked the assistant to draft section text for the notebooks and the final report, then edited and shortened the result. None of the section drafts went into the repository without me reading them through.

### Representative prompt

> *"Final raporu yazalım, guideline'ın istediği 5 bölümle"*

The assistant produced a first draft of the final report (the version you're reading now is the result after one round of expansion and one round of edits). The structure (Motivation → Data Source → Data Analysis → Findings → Limitations) follows the guideline's exact bullet points. The numbers in the Findings tables (15,581 plays; 668 hours; H1-proxy U = 5,552,041.5, p < 0.0001; RF skip ROC-AUC 0.763 ± 0.005; etc.) all come from re-running my own notebooks; the assistant pulled them directly from the notebook outputs and verified them programmatically before writing the paragraph.

### What I changed in the draft

- I asked the assistant to **shorten** several paragraphs that had drifted into "essay" mode rather than "report" mode.
- I rejected an early phrasing that called the project "a contribution to the literature" — it isn't, and that wording felt overclaim-y for a personal-data course project. The assistant rewrote the Motivation section more honestly.
- I added the personal-language touches throughout — e.g., the references to recognizing the Western vs Turkish cluster split in my own listening, and to my exam-period stress not being bounded by the official two-week window. These observations are mine; the assistant turned them into prose.

---

## 6. Category E — One-line clarification questions

The smallest-volume use. Short prompts where I needed a definition or a refresher on a stats concept, mostly during the hypothesis-testing notebook.

Representative one-liners:
- *"Mann-Whitney U ne zaman t-test yerine kullanılır?"*
- *"chi-square contingency table'da expected count 5'ten az olursa ne yapılır?"*
- *"silhouette score 0.5 üstü mü iyi yoksa 0.7?"*

These answers went into my own understanding, not into the repository.

---

## 7. What is *not* the assistant's work

To be specific, because the guideline asks for specificity:

- The **research questions and hypotheses** (H1, H2, RQ2, RQ3) are mine; they were written into the proposal before I began any AI-assisted work.
- The **decision to use my own Spotify data** is mine.
- The **decision to enrich with Last.fm rather than skip H1 entirely** is mine.
- The **choice of four ML models for four questions** is mine; I structured each model to map onto a specific question.
- The **interpretation** of every finding (e.g., "H1 is supported behaviorally but not in its original mood form", "exam periods do not visibly change my daily volume", "weekday-weekend flattening reflects student life") is mine.
- The **limitations** I chose to surface, including the single-user external-validity problem, the outlier-pre-cleaning gap, and the approximate exam-window definition, are mine. They reflect what I think the real weaknesses of the work are.

The assistant is responsible for: notebook scaffolding, the wording of many explanatory paragraphs, debugging help on specific errors, and acting as a sounding board on the methodological adaptation forced by the Spotify API deprecation. None of those is the same as deciding what to study or what the answers mean.

---

## 8. Conversation transcripts

Full chat transcripts are retained in my Claude account history for the duration of the course. If the instructor or course staff would like to review them, I can export and share them on request.

---

*This log was itself drafted with AI assistance and then edited by me for accuracy and tone. The structure (eight sections, categorized by task type) and the specific examples are mine; the prose is a mix of my edits and the assistant's drafting.*
