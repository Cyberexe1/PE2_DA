# Presentation Script 1 — FULL TECHNICAL

**Capstone Project | Data Analytics**
**Title: Indian Agricultural Statistics — Crop Production Forecasting (Experiments 1–10)**

> Speaker script. Numbers below are the actual values produced by the executed notebook
> (`India_Agriculture_Forecasting_LocalCSV_Experiments_1_to_10.ipynb`). Slide cues point to the
> figures in `report_outputs/`. Approx. speaking time: 14–18 minutes.

---

## Slide 1 — Title & problem statement

"Good morning. My capstone is an end-to-end data-analytics and machine-learning pipeline that
forecasts **next-year crop production** for India from historical agricultural statistics.

The whole project is organised as ten experiments that mirror a standard analytics lifecycle:
business objective, data survey, exploratory analysis, feature engineering and dimensionality
reduction, hypothesis testing, model comparison, model development, validation, interpretation,
and finally real-world decision support."

---

## Slide 2 — Experiment 1: Business objective & success criteria

"The business goal is to predict how much of each crop a region will produce next year, so planners
can manage procurement, storage, and monitoring.

I defined the target up front as a **leakage-safe, next-year production** value, and I committed to
judging the model primarily on **RMSE and MAE**, with R² and MAPE as supporting metrics. Model
selection would use only training and validation years; the final test years stay sealed until the end."

*(Cue: `exp1_dataset_scale.png`, `exp1_yearly_observations.png`, `exp1_data_completeness.png`)*

---

## Slide 3 — Experiment 2: Data source & survey

"The data source is a single local file, `crop-wise-area-production-yield.csv`. It's district-level
Indian crop data with 16 columns: identifiers, year, state and district, crop name and crop type,
season, and the three core quantities — **area in hectares, production in tonnes, and yield in
tonnes per hectare**, each with an explicit unit column.

Because it's one harmonised source, I don't do any risky cross-dataset joins. The pipeline is written
to *optionally* enrich with price or weather if those files exist, but here they don't, so those
branches are safely skipped."

*(Cue: `exp2_crop_type_composition.png`, `exp2_state_coverage.png`, `exp2_season_coverage.png`)*

---

## Slide 4 — Experiment 3: EDA & data quality

"After cleaning — dropping impossible values, recomputing yield, converting the `1997-1998` style
year string to an integer start year — I have **450,350 rows**.

Coverage: **1997 to 2022, so 26 crop years, 115 crops, 34 states, 737 districts, 7 seasons, and
10 crop types**. Zero duplicate rows and essentially no missing values.

The distributions are extremely right-skewed — production spans from under one tonne to about
**1.6 billion** at the top end — so I visualise on a log scale. Area, production and yield correlate
as you'd expect, with area and production being the strongest pair."

*(Cue: `exp3_univariate.png`, `exp3_national_trend.png`, `exp3_top_crop_trends.png`, `exp3_correlation.png`)*

---

## Slide 5 — Experiment 4: Feature engineering, selection, PCA

"I aggregate to a **crop × year × season** forecasting table — **4,852 rows, 14 columns** — and
engineer temporal features: lag-1 and lag-2 production, area and yield lags, area/yield/production
growth rates, and a 3-year rolling mean and standard deviation. Every lag and rolling feature is
shifted so the year *t+1* prediction never sees year *t+1* data — that's the leakage rule.

I end up with **11 numeric features**, and all 11 survive the variance-threshold filter.

By **mutual information**, the strongest predictors are **production_lag_1 at 1.19**,
**production_roll_mean_3 at 1.16**, **production_lag_2 at 1.00**, and **area at 0.76** — so recent
history and cultivated area carry most of the signal.

PCA says **6 components explain at least 85%** of the variance, confirming moderate redundancy among
the engineered features."

*(Cue: `exp4_mutual_information.png`, `exp4_pca.png`, `exp4_feature_reduction.png`)*

---

## Slide 6 — Experiment 5: Hypothesis testing

"Three hypotheses, all at alpha = 0.05.

- **H1 — do high-area and low-area observations differ in yield?** Welch's t = 19.6, p ≈ 1.7e-85;
  Mann-Whitney confirms it, p ≈ 1.8e-187. Reject the null — they differ strongly.
- **H2 — does production growth relate to yield?** Welch's t = 13.3, p ≈ 2.8e-40. Significant.
- **H3 — do major crops differ in mean yield?** One-way ANOVA F = 2010.8, p effectively 0.
  Crops are very clearly different.

With sample sizes this large, almost anything is statistically significant, so I'm careful to read
these as evidence of association, not causation."

*(Cue: `exp5_correlation.png`, `exp5_dashboard.png`, `exp5_hypothesis_tests.png`)*

---

## Slide 7 — Experiment 6: Model benchmark

"I split chronologically: **train on 1997–2019, test on 2020–2022**, and benchmark eight regressors —
Linear, Ridge, Lasso, Linear SVR, Random Forest, Extra Trees, Gradient Boosting, and
HistGradientBoosting.

On the sealed test set, ranked by RMSE:
- **Gradient Boosting is best — RMSE 6.31e8, R² 0.549**
- Extra Trees and Random Forest next, R² around 0.50
- the linear family around R² 0.47–0.48
- HistGradientBoosting 0.34
- and **Linear SVR is worst, R² essentially 0** — it can't cope with the scale of this target.

One caveat I flag openly: MAPE is astronomically large here because many actual values are near zero,
which makes percentage error explode. That's exactly why I committed to RMSE and MAE as the primary
metrics."

*(Cue: `exp6_benchmark.png`, `exp6_mae_comparison.png`, `exp6_rmse_r2_tradeoff.png`)*

---

## Slide 8 — Experiment 7: Model development & selection

"For development I compare two finalists on a held-out validation slice: Ridge as the interpretable
linear baseline, and Random Forest for non-linear interactions.

On validation, **Random Forest wins — R² 0.967, RMSE 1.90e8**, just ahead of Ridge at R² 0.964. So
Random Forest is selected. The final test set was *not* used to make this choice."

*(Cue: `exp7_validation_rmse.png`, `exp7_validation_r2.png`, `exp7_chronological_split.png`)*

---

## Slide 9 — Experiment 8: Time-aware validation + the honest gap

"Because this is a time series, I validate with **TimeSeriesSplit** — five folds, each training on
earlier years and validating on later ones.

Cross-validated performance is strong: **R² mean 0.947 ± 0.079**, RMSE mean ≈ 2.0e8.

But here's the honest part. On the **final sealed test years, 2020–2022, R² drops to 0.503**, RMSE
6.63e8. That gap between 0.95 in CV and 0.50 on the final test is a real finding, not something I hide.
Two causes: a handful of giant-scale crops dominate the squared error, and because next-year production
is a forward shift, several 2022 rows have a target of zero at the end of the series, which the model
over-predicts. I'll show that in the next experiment."

*(Cue: `exp8_evaluation.png`, `exp8_cv_rmse.png`, `exp8_error_by_year.png`)*

---

## Slide 10 — Experiment 9: Interpretation

"Permutation importance on the final model highlights **area_growth** as the most useful single
perturbation feature. The large level features — lagged production, yield, the rolling mean — behave
oddly under permutation because they're strongly collinear; so I interpret importance as
*predictive usefulness, not causal influence*. Area is mechanically tied to production, after all.

Crop-level error is very concentrated. **Coconut dominates** — MAE around 7.35e9 from only three
observations, where the model predicts roughly 1.5e10 against some zero 2022 actuals. Then Sugarcane,
Rice, Wheat and Cotton. This tells me the error isn't spread evenly — it's a few extreme, large-scale,
sparsely-sampled crops."

*(Cue: `exp9_importance.png`, `exp9_crop_errors.png`, `exp9_crop_bias.png`)*

---

## Slide 11 — Experiment 10: Decision support + ablation

"Finally I turn the analysis into a decision tool. I build a **priority score =
0.6 × error-risk + 0.4 × production-scale**, min-max normalised per crop. **Coconut scores 1.0** — top
priority for investigation — while everything else sits below 0.01. So the system correctly flags the
one crop a planner should scrutinise before trusting the forecast.

The ablation study compares Baseline vs Baseline+Price vs Baseline+Price+Weather. Here all three are
identical because this single source has no price or weather columns — which is itself a clean,
reproducible result: with current data, there's no external signal to add."

*(Cue: `exp10_decision_matrix.png`, `exp10_production_vs_forecast_risk.png`, `ablation_study.png`)*

---

## Slide 12 — Conclusions, limitations, future work

"To summarise:
- A full, reproducible 10-experiment pipeline on **450k records across 26 years and 115 crops**.
- **Recent production history and area** are the dominant predictors; crops and area-classes differ
  significantly in yield.
- **Random Forest** was selected; **cross-validated R² ≈ 0.95**, but **final-period R² ≈ 0.50**, with
  the gap traced to extreme-scale crops and end-of-series zero targets.

Limitations I'd state plainly: the target is absolute tonnes, so metrics are dominated by the biggest
crops; MAPE is unstable near zero; a few 2022 targets are zero by construction; importance isn't
causation; and it's a single source, so no price or weather enrichment.

Future work: model **log-production or yield** instead of raw tonnes, train **per-crop or per-scale**
models, add price and weather data, and handle the series-end boundary explicitly. Thank you — happy to
take questions."

---

### Appendix — quick-reference numbers
- Rows after cleaning: 450,350 | Forecast table: 4,852 × 14
- Coverage: 1997–2022 · 115 crops · 34 states · 737 districts · 7 seasons · 10 crop types
- MI top: prod_lag_1 1.19 · roll_mean_3 1.16 · prod_lag_2 1.00 · area 0.76 | PCA 85% → 6 comps
- H1 Welch t=19.6 (p≈1.7e-85) · H2 Welch t=13.3 (p≈2.8e-40) · H3 ANOVA F=2010.8 (p≈0)
- Benchmark best RMSE: Gradient Boosting R²=0.549 · RF R²=0.503 · Linear SVR R²≈0
- Selected: Random Forest (val R²=0.967) | CV R²=0.947±0.079 | Test R²=0.503, RMSE=6.63e8, MAE=3.79e7
- Highest-error crop: Coconut (MAE≈7.35e9, 3 obs) | Priority #1: Coconut (score 1.0)
- 31 figures total (3+ per experiment); final report: `India_Agriculture_Experiments_1_to_10_FINAL.docx`
