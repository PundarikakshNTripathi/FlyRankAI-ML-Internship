---
layout: default
title: "Prioritizing SEO Content Interventions via Causal Inference"
---

# Prioritizing SEO Content Interventions via Causal Inference

## Abstract
**Question:** How can we optimally route human editorial capacity to the most impactful content interventions? 
**Data:** We utilize the FlyRank 79-million-row warehouse snapshot, containing strictly anonymized time-series search performance data. 
**Method:** We shift from standard decay classification to Causal Machine Learning by implementing an S-Learner Random Forest to predict the Individual Treatment Effect (ITE) of a content refresh. 
**Result:** The model successfully identifies high-yield pages (e.g., +1,200 incremental impressions) by differentiating causal uplift from mere correlation, drastically outperforming static "stale and visible" heuristics. 
**Application:** This causal scoring system acts as a production-grade action queue, preventing the cannibalization of organic traffic and maximizing the ROI of human editorial hours.

---

## 1. Introduction & Problem Statement
In enterprise SEO, editorial capacity is strictly bounded. A standard heuristic approach dictates that content teams should update pages that are "old and losing traffic." However, this correlation-based approach is dangerously flawed. It leads editors to intervene on pages that are naturally declining due to seasonality, or worse, pages that would have recovered entirely on their own.

A wrong recommendation costs valuable human review time and risks destabilizing already high-performing pages. To solve this, we must ask a causal question: *"If we apply the treatment (refreshing the page), exactly how many incremental clicks will this specific page gain compared to doing nothing?"*

## 2. Data
We built this pipeline on the **FlyRank ML Internship Dataset** (`flyrank_pseudonymized_warehouse_release_v20260703`). 
*   **Scale:** The full warehouse contains 79,835,655 rows of daily search performance across 519,606 pseudonymized content items. 
*   **Windows:** We rely on the trailing 90-day window (`impressions_90d`) for features.
*   **Excluded:** All direct label-derived metrics (e.g., `trend_pct`, `trend_direction`) were rigorously excluded to prevent label leakage. The dataset ships strictly anonymized, meaning no raw text, URLs, or client identifiers were exposed to the model.

## 3. Methodology
Inspired by multi-stage recommender systems deployed at Netflix, we utilize a Causal Inference framework.

**Assumptions & Framing:** 
Instead of predicting decline, we predict Causal Uplift (Incremental Impressions) using an **S-Learner (Single-Learner)** architecture.
*   **Treatment (T):** `is_fresh` (Content updated within the last 180 days).
*   **Outcome (Y):** Log-transformed `impressions_90d`.
*   **Confounders (X):** `word_count`, `avg_position`, `ctr`.

**Validation Design:**
We utilized a `GroupShuffleSplit` on `client_id`. This grouped holdout guarantees that the model has never seen the client's domain structure during training, enforcing true generalization and preventing the memorization of client-specific quirks.

## 4. Results (Causal Uplift vs Baseline)
The S-Learner successfully computes the **Conditional Average Treatment Effect (CATE)** for every stale page. 

While the static heuristic rule (`age > 180 AND impressions > 500`) blindly flags thousands of pages with equal priority, the Causal ML model exposes a massive variance in actual return on investment. The predicted causal uplift distribution reveals a long tail: a small percentage of pages will yield +1,000 incremental impressions if refreshed, while the vast majority will yield near zero.

*(The interactive distribution chart and precise Precision@K metrics are reproducible directly via the Capstone notebook in the repository).*

## 5. Limitations & Honest Framing
This research relies on **observational causal inference**, which assumes *no unobserved confounders*. If pages were historically refreshed due to off-page factors not captured in this dataset (e.g., sudden viral social media spikes, or intensive backlinking campaigns), our uplift estimates may be biased. 

We **cannot claim** that this model perfectly simulates Google's algorithm. Furthermore, we cannot guarantee absolute causal certainty without conducting randomized A/B tests (e.g., refreshing a randomly selected 50% of the candidate queue and holding out the rest).

## 6. Ranked Recommendations (Action Playbook)
The output of this pipeline is a mathematically rigorous action queue. 

**Playbook:**
1.  **Decaying High-Volume (High Uplift):** Route to senior editors for immediate full-scale content expansion and semantic optimization.
2.  **Stale Mid-Tier (Moderate Uplift):** Route to junior editors for metadata verification and structural hygiene.
3.  **No-Go:** Do not automatically canonicalize or delete pages based solely on this score; human-in-the-loop verification is mandatory to check for seasonality.

## 7. Reproducibility
The full data engineering pipeline, causal inference model, and validation harnesses are available in the public repository:
*   [View the GitHub Repository](https://github.com/PundarikakshNTripathi/FlyRankAI-ML-Internship)
*   [View the Capstone Notebook](https://github.com/PundarikakshNTripathi/FlyRankAI-ML-Internship/blob/main/work/notebooks/capstone.ipynb)

## 8. Acknowledgments & Data Credit
This research was built on the incredible **FlyRank ML Internship Dataset**. 
Special thanks to the data engineering and product teams for providing rigorous, real-world search intelligence data.
Data Source: [https://flyrank.ai](https://flyrank.ai)
