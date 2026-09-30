# Capstone Promos & Shareables

## Demo Outline (5 Minutes)
1. **The Problem:** Most SEO refresh strategies rely on basic heuristics (like updating anything older than 6 months). This creates a lot of wasted effort on pages that are naturally declining due to seasonality, or pages that would have recovered anyway.
2. **The Methodology:** I framed this as a causal inference problem rather than standard classification. I used DuckDB to pull 79 million rows of daily search data from the Hugging Face warehouse.
3. **The Model:** I built an X-Learner architecture using Scikit-Learn. Instead of just predicting if a page is decaying, the X-Learner isolates the control and treatment groups to predict the actual *incremental impressions* (the causal uplift) we'd get if we intervened.
4. **The Evaluation:** Since we can't A/B test in the past, I evaluated the model using a Qini curve on a grouped holdout set to verify the targeting efficiency.
5. **The Business Value:** The model generates a prioritized daily action queue. Comparing the model's top 50 recommendations against the legacy heuristic showed a significant efficiency gain in projected impressions for the exact same amount of editorial hours.

## Executive Summary (FlyRank AI)
For my capstone project, I built a causal inference pipeline to optimize SEO content updates using 79 million rows of search performance data. I implemented an X-Learner architecture to predict the incremental traffic uplift of refreshing a page, rather than just classifying historical decay. This approach isolates high-ROI pages and provides the editorial team with a prioritized, data-driven action queue that significantly outperforms basic age-based heuristics.

## Project Retrospective
I just wrapped up my Machine Learning Capstone, and I’m pretty excited about the results. I was tasked with figuring out which SEO pages an editorial team should prioritize for content refreshes.

Instead of building a standard classifier to predict which pages were losing traffic, I framed it as a causal inference problem: *If we spend the hours to update this specific page, what is the actual incremental traffic we will get back?*

I used DuckDB to process 79M rows of search data and built an X-Learner pipeline in Scikit-Learn. By predicting the Conditional Average Treatment Effect (CATE) and weighting it with a propensity score, the model outputs a prioritized action queue. When evaluated with a Qini curve, the causal model showed a strong efficiency gain over standard age/traffic heuristics. Really enjoyed diving into causal ML for this one!
