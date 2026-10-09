# Kaso Annotation Codebook

**Version:** 0.1 (initial draft) · **Status:** not yet piloted with annotators

This codebook defines how Hiligaynon code-switched (Hiligaynon–Tagalog–English) restaurant reviews are annotated for Kaso. Each review is labeled with zero or more tuples:

```text
(Aspect Term, Aspect Category, Pragmatic Sentiment)
```

All Hiligaynon examples below are illustrative and must be checked by native-speaker annotators during the pilot (see [Open Questions](#open-questions)).

---

## 1. Annotation Unit

- The unit is one **review** (the full text of one Google Maps review).
- A review may yield **0, 1 or many tuples**. Annotate every distinct aspect that receives an opinion.
- Reviews are annotated exactly as written. Do not correct spelling or translate.
- Star ratings are **not** shown to annotators, so labels reflect the text only.

## 2. Aspect Term

The span of text that names the thing being evaluated.

| Rule | Example |
| :--- | :--- |
| Copy the **shortest span** that names the aspect, exactly as written. | *"ang batchoy"* → `batchoy` |
| Omit articles and determiners (*ang*, *sang*, *the*). | `serbisyo`, not `ang serbisyo` |
| Keep multi-word names together. | `fried chicken`, `second floor` |
| A verb-form noun counts as a term. | `pag-serve` |
| If the aspect is **implied but not named**, use `NULL` and still assign a category. | *"Dugay gid!"* → (`NULL`, Service & Waiting Time, Hard Complaint) |
| Do not annotate the opinion word as the aspect. | In *"namit ang batchoy"*, the term is `batchoy`, not `namit`. |
| If the same aspect is mentioned twice with the same label, annotate it once. | |

## 3. Aspect Categories

Assign **exactly one** category per tuple. Initial set (to be refined after the pilot):

| Category | Covers |
| :--- | :--- |
| **Food & Beverage** | Taste, quality, portion size, freshness, menu items, temperature of food |
| **Service & Waiting Time** | Staff behavior, speed of serving, order accuracy, waiting, reservations |
| **Price & Value** | Cost, affordability, value for money, billing |
| **Ambiance & Facilities** | Cleanliness, seating, aircon/fans, noise, decor, restrooms, parking |
| **Location & Accessibility** | Where it is, how easy it is to reach, directions |
| **Overall Experience** | General opinion with no specific aspect (*"Ok man ang resto"*) |
| **Other** | Anything relevant to the business that fits nowhere above |

Decision rules:
- If a phrase fits two categories, choose the category the **customer's complaint or praise is actually about**. *"Mahal ang batchoy"* is **Price & Value**. *"Gamay ang batchoy"* is **Food & Beverage**, because it is about portion size.
- Use **Overall Experience** only when no specific aspect can be identified.

## 4. Pragmatic Sentiment

Assign **exactly one** label per tuple.

| Label | Definition | Example |
| :--- | :--- | :--- |
| **Positive** | Explicit praise or satisfaction | *"Namit gid ang lasa sang ila chicken!"* |
| **Soft Complaint** | Dissatisfaction that is hedged, mitigated or indirect | *"Namit man tani kaso medyo gamay ang portion."* |
| **Hard Complaint** | Direct negative criticism with no softening | *"Kalaw-ay sang serbisyo, rude sang staff!"* |
| **Constructive Suggestion** | An actionable recommendation or wish for improvement | *"Tani magdugang sila fan sa second floor."* |
| **Neutral** | Factual statement with no evaluation | *"Nagbukas ni sila sang bag-o nga branch."* |

### 4.1 Deciding Soft vs. Hard Complaint

A complaint is **Soft** if the dissatisfaction is expressed with at least one of these:

| Signal | Typical markers | Example |
| :--- | :--- | :--- |
| **Hedge / downtoner** | *medyo*, *bahin*, *slight*, *a bit*, *kinda* | *"Medyo dugay ang pag-serve."* |
| **Contrast (praise → complaint)** | *kaso*, *pero*, *tani*, *but* | *"Okay man ang service, kaso dugay."* |
| **Politeness / mitigation** | *basi*, *siguro*, *maybe*, *sana* (as softener) | *"Basi mas maayo kon mas madasig."* |
| **Understatement / indirectness** | Negated positives, rhetorical wording | *"Indi man gid kapin ka-namit."* |

A complaint is **Hard** if it states the problem plainly with no softener, uses strong negative words, or insults, or uses intensifiers on the negative (*gid*, *grabe*, *super*, *sobra*).

If the same sentence mixes both, label by the **complaint clause**, not the praise clause. In *"Namit man tani kaso medyo gamay ang portion"*, the food is praised and the portion is a Soft Complaint, so annotate both.

### 4.2 Complaint vs. Suggestion

- If the text only **states a problem**, it is a Complaint (Soft or Hard).
- If the text **proposes or wishes for a change**, it is a Constructive Suggestion, even when it implies a problem.
- If it does both (*"Dugay ang serve, tani dugangan nila ang staff"*), annotate **two tuples**: a Complaint for the problem and a Suggestion for the proposal.

### 4.3 Neutral

Use **Neutral** only for factual statements about the business (opening, location, menu facts, visit circumstances). Do not use it for vague opinions. When a review has no evaluable content at all, annotate no tuples (see §6).

## 5. Special Cases

| Case | Guideline |
| :--- | :--- |
| **Code-switching** | Annotate regardless of language mix. Hedge markers count in any of the three languages. |
| **Sarcasm** | Label the **intended** meaning, not the literal one. If you cannot tell, use the literal reading and set `uncertain: true`. |
| **Mixed praise and complaint on one aspect** | *"Namit pero mahal."* is two aspects: food (Positive) and price (Complaint). For one aspect with conflicting statements, label the **final** opinion. |
| **Comparisons** | *"Mas namit sa iban nga resto"* is Positive for the reviewed place. Do not annotate the other place. |
| **Owner responses** | Not annotated. Only the customer's text is used. |
| **Emojis and ratings in text** | Treat emojis as evidence for sentiment, but do not annotate an emoji as an aspect term. |
| **Repeated or spam text** | See §6. |

## 6. Exclusions

Do **not** annotate tuples and mark the review `skip: true` with a reason when:

- The review is empty or only a star rating.
- The text is unreadable, spam, advertising or a link.
- The text is entirely in a language other than Hiligaynon, Tagalog or English (reason: `other_language`).
- The review is clearly about a different business.

Reviews written fully in English or Tagalog, with no Hiligaynon, are still annotated, but flag them with `language_note` so the share of Hiligaynon content can be measured later.

## 7. Annotation Format

One JSON object per review, stored in `data/processed/`:

```json
{
  "review_id": "ChZDSUhNMG9nS0VJQ0FnSUN...",
  "text": "Namit man tani ang batchoy nila, kaso lang medyo dugay ang pag-serve.",
  "skip": false,
  "skip_reason": null,
  "uncertain": false,
  "language_note": null,
  "tuples": [
    {"aspect_term": "batchoy", "category": "Food & Beverage", "sentiment": "Positive"},
    {"aspect_term": "pag-serve", "category": "Service & Waiting Time", "sentiment": "Soft Complaint"}
  ]
}
```

- `review_id` links back to `data/raw/google_maps_reviews.csv`.
- Use `"aspect_term": "NULL"` for implicit aspects.
- Set `uncertain: true` for any review where you were unsure; these are reviewed in adjudication.

## 8. Quality Control

1. **Pilot:** at least 2 annotators label the same 100 reviews, then meet to resolve disagreements and update this codebook.
2. **Inter-annotator agreement (IAA):** measured with Cohen's κ (2 annotators) or Fleiss' κ (3+) using `src/iaa_calculator.py`, reported separately for aspect category and pragmatic sentiment. Target: κ ≥ 0.70 on both before full annotation.
3. **Adjudication:** disagreements and `uncertain` items are resolved by discussion, with a third annotator breaking ties.
4. **Versioning:** any change to labels or rules increments the version below and is logged.

## 9. Open Questions

To resolve during the pilot:

- Are the hedge markers in §4.1 correct and complete for Ilonggo usage? Which Hiligaynon markers are missing?
- Should **Overall Experience** be kept as a category, or split off as a separate document-level label?
- Should **Location & Accessibility** be kept? Google Maps reviews may rarely mention it.
- How should reviews that are mostly English be handled, if they are the majority of the scraped data?
- Is `Neutral` frequent enough in reviews to keep as a class?

## Changelog

| Version | Date | Changes |
| :--- | :--- | :--- |
| 0.1 | 2026-10-09 | Initial draft based on the taxonomy in the README |
