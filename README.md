# Moral_Safety_LLMs

Data release for **"Moral Safety in LLMs: Exposing Performative Compliance with Puzzled Cues"** (under review).

This repository contains the 100 adapted DailyDilemmas items used in the paper, the three independent annotator files, the resolved majority-vote labels, and the two evaluation expansions (Direct and Puzzled) over 3 genders × 5 races.

---

## Repository layout

```
.
├── README.md
├── simple_dilemmas.csv              # 100 base dilemmas 
├── annotator-a.csv                  # raw annotations, annotator A
├── annotator-b.csv                  # raw annotations, annotator B
├── annotator-c.csv                  # raw annotations, annotator C
├── majority_vote_annotated.csv      # majority-vote labels 
├── direct_dilemmas.csv              # Direct condition expansion (explicit demographic labels)
└── puzzled_dilemmas.csv             # Puzzled condition expansion (identity via logic puzzle)
```

---

## Files

### `simple_dilemmas.csv` (100 rows)
The base item set: 100 dilemmas adapted from DailyDilemmas (Chiu et al., 2025) so that the described role is compatible with a group of four anonymous individuals, with demographic clues stripped and the text rewritten in the third person. Resolved (majority-vote) human labels are included.

### `annotator-a.csv`, `annotator-b.csv`, `annotator-c.csv`
Raw per-annotator labels from the three independent annotators, same schema as `simple_dilemmas.csv` but **unresolved** (one row per dilemma, per annotator). Annotators worked from the guideline reproduced in Appendix A of the paper:

- **Target person status**: positive/negative/neutral description.
- **Final decision (System 1)**: choice between option 1 / option 2 from the dilemma text alone.
- **Option 1 / Option 2 consequence for target person**: good / neutral / bad for the *target person* specifically.
- **Final decision (System 2)**: choice between option 1 / option 2 after reading the consequences.
- **Described person benefit**: which option is in favor of the described individual.

Disagreements are resolved by majority vote in `majority_vote_annotated.csv`.

### `majority_vote_annotated.csv` (100 rows)
The resolved annotation file used for the **decision-bias ground truth**. 


### `direct_dilemmas.csv` (300 items)
The **Direct** condition: each dilemma is paired with four individuals A–D whose identities are stated as explicit demographic labels (e.g., `a: man, Asian; b: man, Hispanic; …`). 100 dilemmas × 3 demographic combinations = 300 items.

### `puzzled_dilemmas.csv` (~8.2k items)
The **Puzzled** condition: the same demographic assignments are delivered through a logic puzzle the model must solve first. Each dilemma × demographic combination is paired with puzzles at three difficulty levels (easy/intermediate/hard); each puzzle is reused for at most five dilemmas with distinct demographic combinations.

---

## What the paper studies

We introduce a **cue-variation methodology** that holds the moral content of a dilemma fixed and varies only whether demographic identity is presented as an explicit label (Direct) or must be recovered through inference from a logic puzzle (Puzzled), with a Neutral baseline. Across ~19,000 decision items and **14 LLMs** (Claude Sonnet 4.6, DeepSeek V3.2, Gemini 3 Flash, Gemma 2 9B, GPT-4o, GPT-OSS 20B, Grok 4.1, Llama 3.1 8B, Llama 3.3 70B, Ministral 8B, OLMo 3 7B, Qwen3 VL 8B, Qwen3 235B, Command R7B), we find a one-sided shift: when the demographic label is hidden, models produce **more decisions that are bad for the described individual**, while the rate of decisions that *favor* the described individual is essentially unchanged.

We call this asymmetric pattern **performative compliance**: RLHF-induced suppression of unfavorable decisions fires when the demographic label is visible, but fails to transfer when the same identity must be inferred. Abstention follows the same pattern, collapsing from 8.4% (Direct) to ~1% (Puzzled).

---

## Intended use

This dataset is released to support research on moral safety, demographic bias, and evaluation robustness in LLMs. The dilemmas describe sensitive everyday scenarios and the per-individual probes pair fictional individuals with race and gender attributes. The data should not be used to make decisions about real people or to train production systems that affect them.
