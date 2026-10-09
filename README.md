# Kaso: Aspect-Based Soft Complaint Analysis for Hiligaynon Reviews

<!-- [![License: CC BY-NC-SA 4.0](https://img.shields.io/badge/License-CC_BY--NC--SA_4.0-lightgrey.svg)](https://creativecommons.org/licenses/by-nc-sa/4.0/)
[![Python 3.10+](https://img.shields.io/badge/python-3.10%2B-blue.svg)](https://www.python.org/downloads/)
[![Framework: PyTorch / HuggingFace](https://img.shields.io/badge/Framework-HuggingFace_|_PyTorch-orange.svg)](https://huggingface.co/) --> 

Kaso is a Natural Language Processing (NLP) framework designed for Aspect-Based Sentiment and Intent Analysis (ABSA), with a focus on detecting Soft Complaints (hedged or indirectly expressed dissatisfaction) and Constructive Suggestions in Hiligaynon code-switched customer reviews (Hiligaynon-Tagalog-English).

Traditional sentiment analysis approaches primarily focus on identifying overall positive, negative, or neutral sentiment. However, customer dissatisfaction is not always expressed through explicitly negative language. Reviews may contain hedging, mitigation, contrastive expressions, sarcasm, and indirect phrasing, making the underlying complaint difficult to identify through sentiment polarity alone. For example, a review such as "Okay man tani ang service, kaso medyo dugay lang" may express dissatisfaction with service time despite containing predominantly positive or neutral language.

Kaso aims to address this gap by extracting fine-grained aspect tuples and assigning pragmatic intent labels, enabling the identification of what aspect of a service or product is being discussed and whether the review expresses a soft complaint, constructive suggestion, or other relevant intent.

---

## Task Formulation

Given an input review, the pipeline extracts structured tuples in the format:

$$\text{Output Tuple} = (\text{Aspect Term}, \text{Aspect Category}, \text{Pragmatic Sentiment})$$

### Example

* **Input Review:** `"Namit man tani ang batchoy nila, kaso lang medyo dugay ang pag-serve."`
* **Extracted Tuples:**
  1. `("batchoy", "Food & Beverage", "Positive")`
  2. `("pag-serve", "Service & Waiting Time", "Soft Complaint")`

---

## Pragmatic Taxonomy

| Sentiment / Intent Class | Description | Localized Example |
| :--- | :--- | :--- |
| **Positive** | Explicit praise or satisfaction | *"Namit gid ang lasa sang ila chicken!"* |
| **Soft Complaint** | Indirect/hedged negative criticism (*medyo*, *kaso*) | *"Namit man tani kaso medyo gamay ang portion."* |
| **Hard Complaint** | Direct negative reproach without softeners | *"Kalaw-ay sang serbisyo, rude sang staff!"* |
| **Constructive Suggestion** | Actionable recommendation or wish statement | *"Tani magdugang sila fan sa second floor."* |
| **Neutral** | Objective statement devoid of sentiment | *"Nagbukas ni sila sang bag-o nga branch."* |

---

## Repository Structure

```text
kaso/
├── data/
│   ├── raw/                 # Unannotated review corpora
│   ├── processed/           # Formatted train/val/test datasets
│   └── codebook/            # Annotation guidelines & taxonomy specs
├── notebooks/               # Data exploration & error analysis notebooks
├── models/                  # Fine-tuning scripts (mDeBERTa, XLM-RoBERTa)
├── src/
│   ├── preprocessing.py     # Text cleaning & tokenization utilities
│   ├── iaa_calculator.py    # Cohen's / Fleiss' Kappa calculation tools
│   └── evaluate.py          # F1, Precision, and Recall metrics
├── requirements.txt         # Project dependencies
├── LICENSE
└── README.md