# MMAI Capstone Project — Auto-Tagging Customer Feedback (Multi-Label NLP)

## Summary

This project builds an end-to-end pipeline to **automatically classify free-text customer comments** into a two-level taxonomy:

* **Broad Code 1** (top-level category)
* **Sub Code 1** (more granular sub-category)

It includes data ingestion from Excel (multi-sheet), text cleaning, EDA, and two modeling approaches: a **shallow ML pipeline (TF-IDF + RandomForest via MultiOutputClassifier)** and a **Keras deep-learning baseline**.

---

## Goal

* **Automate manual coding** of customer feedback to speed up reporting and reduce human effort.
* **Improve consistency** of tags for downstream analytics and operational triage.
* Provide a **reproducible notebook** others can run and extend.

---

## Procedure

1. **Data Ingestion**

   * Read all sheets from `SoF Q1–Q4 2018–2021 Comments - coded.xlsx`; concatenate to one DataFrame.
   * Combine `tab name` + `Comment` into a single text feature.

2. **Text/Data Cleansing**

   * Lowercasing, HTML/emoji/punctuation removal, stop-word removal, lemmatization.
   * Null/duplicate handling and basic class distribution checks.

3. **EDA**

   * Class balance review and top n-grams per class to understand signal and leakage risks.

4. **Modeling**

   * **Shallow ML:** TF-IDF vectorization → `MultiOutputClassifier(RandomForest)`, tuned with `GridSearchCV`.
   * **Deep Learning:** Keras multi-output network predicting Broad and Sub codes jointly.
   * **Split:** `train_test_split(test_size=0.3)`; evaluate on the held-out set.

5. **Evaluation**

   * Primary metrics: **Accuracy** and **F1** for each output head (Broad / Sub).

---

## Results (current best)

**Shallow ML (TF-IDF + RandomForest; best params `max_depth=None, n_estimators=50`)**

* **Broad Code 1:** Accuracy **0.852**, F1 **0.840**
* **Sub Code 1:** Accuracy **0.418**, F1 **0.362**

**Deep Learning (Keras)**

* **Broad Code 1:** Accuracy **0.745**, F1 **0.689**
* **Sub Code 1:** Accuracy **0.260**, F1 **0.137**

**Takeaways**

* With the current dataset and preprocessing, the **shallow ML pipeline outperforms the DL baseline**, especially on the harder **Sub Code** task.
* Sub-category prediction is **class-imbalanced and harder**, suggesting the need for richer features, more data, or hierarchy-aware training.

---

## Business Impact

* **Faster insight turnaround:** Auto-tagging can cut manual review time by **60–80%** (team-dependent), accelerating weekly/monthly reporting.
* **Consistent taxonomy usage:** Reduces variance in human coding, improving the **quality of CX dashboards** and **root-cause analysis**.
* **Operational triage:** Real-time tagging enables **routing to the right teams** (e.g., billing vs. product vs. support) and **earlier detection of emerging issues**.

---

## Next Steps to Make It Better

1. **Handle Class Imbalance**

   * Use class-weighted losses or sampling (e.g., **SMOTE** / stratified sampling); tune decision thresholds per class.

2. **Hierarchy-Aware Modeling**

   * Two-stage model: predict **Broad** first, then **Sub** conditional on Broad; or joint models with hierarchical constraints.

3. **Stronger Text Representations**

   * Fine-tune a **DistilBERT/BERT** model with multi-label heads; compare against TF-IDF RF.
   * Add **bi-/tri-grams**, character features, and domain lexicons.

4. **Robust Evaluation**

   * **Stratified CV** by class; add **per-class precision/recall**, confusion matrices, and error slices by `tab name`.

5. **Data/Label Quality**

   * Audit noisy labels; create **active-learning** loop to prioritize uncertain examples for human review.

6. **Productionization**

   * Export a `predict(text)` function + saved artifacts, add **inference notebook/API**, and a lightweight **monitoring dashboard** (volume, drift, top terms).

---

## How to Run (quick)

```bash
# create env
python -m venv .venv && source .venv/bin/activate
pip install -U pip jupyter pandas numpy scikit-learn matplotlib nltk keras tensorflow

# start notebook
jupyter notebook Capstone_Project_0811.ipynb
```

> Replace packages/versions per your environment; add a `requirements.txt` once finalized.

---

## Repository

* Primary artifact: `Capstone_Project_0811.ipynb`
* Suggested local folders (gitignored): `data/` for raw files, `figures/` for exports.

If you share the exact taxonomy names or want per-class tables/plots included, I can add them and generate a polished `requirements.txt` from the notebook.



