# SDG-Classifier-SFT-GSPO
# SDG Classifier: Fine-Tuning Qwen3-4B with SFT & GSPO

**Course**: AAI 646 | Stevens Institute of Technology  
**Authors**: Tilak Chandana, Palak Sood

---

## Overview

This project fine-tunes a **Qwen3-4B-Instruct** model to classify text into UN Sustainable Development Goals (SDGs 1–16, or "No SDG") using:
- **Supervised Fine-Tuning (SFT)** on ChatML-formatted data
- **GSPO** (Group Supervised Policy Optimization) for reward-based refinement
- A **multi-LLM ensemble voting pipeline** to construct silver-standard training labels

---

## Pipeline (Follow in Order)

| Step | Notebook | Description |
|------|----------|-------------|
| 1 | `notebooks/01_sdg_classifier_grok_3_mini.ipynb` | Use Grok-3-mini + other LLMs to label data with SDG classes |
| 2 | `notebooks/02_voting_to_decide_sdg_labels.ipynb` | First pass voting logic to reconcile LLM disagreements |
| 3 | `notebooks/03_final_voting.ipynb` | Final refined voting logic (trust-weighted, NOSDG-aware) |
| 4 | `notebooks/04_finetune_data_preparer.ipynb` | Convert voted labels into ChatML JSONL for SFT |
| 5 | `notebooks/05_AAI_646_SDG_SFT_GSPO_Final_Project_Tilak.ipynb` | Fine-tune Qwen3-4B on Google Colab (A100) using Unsloth + LoRA |

---

## Data

- `data/split_1_final_classification.xlsx` — Raw spreadsheet with per-LLM SDG votes (columns: `gpt_5_mini`, `gpt_5_nano`, `grok_3_mini`, `grok_4_fast`, `phi_4_mini`, `primary_sdg`)
- `data/data_to_finetune_split_1_final_classification_SFT_ChatML.jsonl` — Final training-ready JSONL in ChatML format

---

## Voting Logic (Steps 2 & 3)

The voting system uses a **trust-weighted majority vote**:
- **High Trust** (human labels, SDG 1–16): Primary label is kept; additional SDGs added if ≥ 2 LLMs agree
- **Low Trust** (primary is NOSDG or SDG17): Requires ≥ 3 total votes; NOSDG wins all ties

---

## Model & Hardware

- **Base Model**: `Qwen/Qwen3-4B-Instruct`
- **Method**: LoRA fine-tuning (16-bit bfloat16) via [Unsloth](https://github.com/unslothai/unsloth)
- **Hardware**: Google Colab A100 GPU

---

## Report

See `report/AAI_646_Final_Project_Report_Tilak_Chandana_Palak_Sood.docx` for the full write-up.
