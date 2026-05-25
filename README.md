# Moral_Safety_LLMs

Data release for **"Performative Compliance: Asymmetric Decision Bias in LLMs when Demographic Cues are Hidden"** (EMNLP 2026, under review).

This repository contains the 100 adapted DailyDilemmas items used in the paper, the three independent annotator files, the resolved majority-vote labels, and the two evaluation expansions (Direct and Puzzled) over 3 genders × 5 races and 14 LLMs.

---

## Repository layout

```
.
├── README.md
├── simple_dilemmas.csv              # 100 base dilemmas + resolved human labels
├── annotator-a.csv                  # raw annotations, annotator A
├── annotator-b.csv                  # raw annotations, annotator B
├── annotator-c.csv                  # raw annotations, annotator C
├── majority_vote_annotated.csv      # majority-vote labels + 3-LLM votes for Described Person Benefit
├── direct_dilemmas.csv              # Direct condition expansion (explicit demographic labels)
└── puzzled_dilemmas.csv             # Puzzled condition expansion (identity via logic puzzle)
```

---

## Files

### `simple_dilemmas.csv` (100 rows)
The base item set: 100 dilemmas adapted from DailyDilemmas (Chiu et al., 2025) so that the described role is compatible with a group of four anonymous individuals, with demographic clues stripped and the text rewritten in the third person. Resolved (majority-vote) human labels are included.

| Column | Description |
|---|---|
| `dilemma_ID`, `ID`, `idx` | item identifiers |
| `dilemma_situation` | dilemma text |
| `person` | short description of the target person |
| `question` | the question posed to the model ("Which option should be followed?") |
| `action_1`, `action_2` | the two options |
| `negative_consequence_1`, `negative_consequence_2` | negative consequence associated with each option |
| `Target_person_status` | `good` / `bad` / `neutral` — whether the target person description leans positive, negative, or neither (Fleiss κ = 0.71) |
| `Option1_consequence_for_target_person` | `good` / `neutral` / `bad` — whether Option 1 is good for the target person |
| `Option2_consequence_for_target_person` | analogous for Option 2 |
| `Final_decision_sys1` | majority decision from the dilemma text alone (no consequences read) |
| `Final_decision_sys2` | majority decision after reading both consequences |

### `annotator-a.csv`, `annotator-b.csv`, `annotator-c.csv`
Raw per-annotator labels from the three independent annotators, same schema as `simple_dilemmas.csv` but **unresolved** (one row per dilemma, per annotator). Annotators worked from the guideline reproduced in Appendix A of the paper:

- **Target person status**: positive / negative / neutral description.
- **Final decision (System 1)**: choice between option 1 / option 2 from the dilemma text alone.
- **Option 1 / Option 2 consequence for target person**: good / neutral / bad for the *target person* specifically.
- **Final decision (System 2)**: choice between option 1 / option 2 after reading consequences.
- **Described person benefit**: which option is in favor of the described individual.

Disagreements are resolved by majority vote in `majority_vote_annotated.csv` and `simple_dilemmas.csv`.

### `majority_vote_annotated.csv` (100 rows)
The resolved annotation file used for the **decision-bias ground truth**. In addition to the human columns, it contains votes from three frontier LLMs (Claude Sonnet 4.6, GPT-4o, DeepSeek V3.2) on the *Described Person Benefit* question; `majority_vote_best_for_person` is the final ground-truth label used by the paper.

| Column | Description |
|---|---|
| `dilemma_situation`, `person`, `question`, `action_1`, `action_2`, `negative_consequence_*` | dilemma fields (see above) |
| `Target_person_action_status` | resolved target person status |
| `Option1_consequence_for_target_person`, `Option2_consequence_for_target_person` | per-option consequence labels |
| `Final_decision`, `Final_decision_sys2` | resolved System-1 / System-2 decisions |
| `vote_claude-sonnet-4-6`, `vote_gpt-4o-2024-08-06`, `vote_deepseek-v3.2` | LLM votes for Described Person Benefit |
| `majority_vote_best_for_person` | **ground-truth label** (`option1` / `option2`) used for the decision-bias metric (Fleiss κ across humans = 0.92) |

### `direct_dilemmas.csv` (300 items)
The **Direct** condition: each dilemma is paired with four individuals A–D whose identities are stated as explicit demographic labels (e.g., `a: man, Asian; b: man, Hispanic; …`). 100 dilemmas × 3 demographic combinations = 300 items.

| Column | Description |
|---|---|
| `dilemma_ID`, `ID`, `idx`, `selection_id` | identifiers |
| `dilemma_situation`, `person`, `question1`, `question2` | dilemma + the two probe questions (Could-be, What-if) |
| `topic_group` | topic category for the topic-breakdown analysis |
| `action_1`, `action_2`, `negative_consequence_1`, `negative_consequence_2` | options and consequences |
| `selected_race1`, `selected_race2`, `selected_gender1`, `selected_gender2` | the two race × two gender values for individuals A–D |
| `Target_person_status`, `Final_decision_sys1`, `Final_decision_sys2` | resolved human labels carried over from the base item |

### `puzzled_dilemmas.csv` (~8.2k items)
The **Puzzled** condition: the same demographic assignments are delivered through a logic puzzle the model must solve first. Each dilemma × demographic combination is paired with puzzles at three difficulty levels (easy / intermediate / hard); each puzzle is reused for at most five dilemmas with distinct demographic combinations.

Additional columns vs. `direct_dilemmas.csv`:

| Column | Description |
|---|---|
| `puzzle` | the puzzle text presented to the model |
| `puzzle_id` | unique puzzle identifier |
| `puzzle_level` | `easy` / `intermediate` / `hard` (mean cognitive-load weight per clue) |

Puzzle solutions are formally verifiable, allowing the paper to restrict analysis to correctly solved items and rule out information loss as an explanation for the observed behavioral shift.

---

## What the paper studies

We introduce a **cue-variation methodology** that holds the moral content of a dilemma fixed and varies only whether demographic identity is presented as an explicit label (Direct) or must be recovered through inference from a logic puzzle (Puzzled), with a Neutral baseline. Across ~19,000 decision items and **14 LLMs** (Claude Sonnet 4.6, DeepSeek V3.2, Gemini 3 Flash, Gemma 2 9B, GPT-4o, GPT-OSS 20B, Grok 4.1, Llama 3.1 8B, Llama 3.3 70B, Ministral 8B, OLMo 3 7B, Qwen3 VL 8B, Qwen3 235B, Command R7B), we find a one-sided shift: when the demographic label is hidden, models produce **more decisions that are bad for the described individual**, while the rate of decisions that *favor* the described individual is essentially unchanged.

We call this asymmetric pattern **performative compliance**: RLHF-induced suppression of unfavorable decisions fires when the demographic label is visible, but fails to transfer when the same identity must be inferred. Abstention follows the same pattern, collapsing from 8.4% (Direct) to ~1% (Puzzled).

### Annotation quality

The annotated labels in this repository come from three independent human annotators with disagreements resolved by majority vote.

| Column | Fleiss κ | Reading |
|---|---|---|
| Target Person Status | 0.71 | substantial |
| Final Decision (System 1) | ~0.32 | moderate (reflects moral ambiguity) |
| Final Decision (System 2) | ~0.36 | moderate |
| Described Person Benefit | **0.92** | almost perfect — used as ground truth |

The near-perfect agreement on Described Person Benefit validates its use as ground truth for the decision-bias metric: *what is good for the described individual* is far less ambiguous than what is ethically right overall.

---

## Intended use

This dataset is released to support research on moral safety, demographic bias, and evaluation robustness in LLMs. The dilemmas describe sensitive everyday scenarios and the per-individual probes pair fictional individuals with race and gender attributes. The data should not be used to make decisions about real people or to train production systems that affect them.

---

## Citation

If you use this data, please cite:

```bibtex
@inproceedings{moralsafetyllms2026,
  title={Performative Compliance: Asymmetric Decision Bias in LLMs when Demographic Cues are Hidden},
  author={...},
  booktitle={Proceedings of EMNLP},
  year={2026}
}
```

And the underlying DailyDilemmas dataset:

```bibtex
@inproceedings{chiu2025dailydilemmas,
  title={DailyDilemmas: Revealing Value Preferences of LLMs with Quandaries of Daily Life},
  author={Chiu, Yu Ying and others},
  booktitle={Proceedings of ICLR},
  year={2025}
}
```
