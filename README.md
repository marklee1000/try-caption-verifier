# Caption Verifier — Streamlit App

A web UI that scores social-media captions on **grammar**, **tone**, and
**inclusivity**, then surfaces platform-specific **recommendations**.

---

## Folder layout expected

```
project/
├── app.py
├── requirements.txt
└── models/
    ├── grammar_t5_model/       ← from Notebook 1 (grammar_t5_model/)
    ├── grammar_artefacts.pkl   ← from Notebook 1
    ├── tone_model_dir/         ← from Notebook 2 (tone_model_dir/)
    ├── tone_artefacts.pkl      ← from Notebook 2
    ├── inclusivity_model_dir/  ← from Notebook 3 (inclusivity_model_dir/)
    └── deployment_meta.pkl     ← from Notebook 3 (preferred — contains all thresholds)
```

If `deployment_meta.pkl` is present it is used. Otherwise the app falls back
to the individual `grammar_artefacts.pkl` and `tone_artefacts.pkl` files
(inclusivity threshold will default to 0.5 in that case).

---

## Installation

```bash
pip install -r requirements.txt
```

LanguageTool also requires Java 8+ to be installed on your machine.

---

## Running

```bash
streamlit run app.py
```

---

## Scores explained

| Score | What it shows |
|-------|---------------|
| **Grammar (raw)** | Raw blended score from T5 similarity + LanguageTool (0–1). **Not scaled** — this is the direct output as requested. |
| **Tone** | Scaled 0–100 via sigmoid around the learned threshold. ≥70 = pass. |
| **Inclusivity** | Same scale as tone. |
| **Overall** | Simple mean of grammar×100, tone, inclusivity (all on 0–100). |

---

## Recommendation rules

| Rule | Trigger |
|------|---------|
| Tone warning | tone raw score < threshold |
| Inclusivity warning | inclusivity raw score < threshold |
| Gender-bias warning | inclusivity low **and** gendered words found |
| Abbreviation warning | 2+ uppercase letters not spelled out first (NYC always allowed) |
| CALL FOR APPLICATIONS hashtags | Text contains "CALL FOR APPLICATIONS" but missing required hashtags |
| Facebook lead-text | Platform = Facebook + non-Other post type + caption doesn't start with required prefix |
| Twitter character limit | Platform = Twitter/X + caption > 280 chars |
| Twitter hashtags | Platform = Twitter/X + missing #ForTheFilipinoYouth or #ParaSaKabataangPilipino |
| TikTok hashtags | Platform = TikTok + missing any of the 5 required tags |
| Instagram hashtags | Platform = Instagram + none of the 5 approved hashtags present |
