# Model Card - Calibrated XGBoost for 24-hour Tropical Cyclone RI Prediction
> The outline follows Figure 1 of [Mitchell et al., Model Cards for Model Reporting (2019)](https://arxiv.org/abs/1810.03993).
> Numbers are from a run of the tutorial notebook. Because XGBoost trains in parallel, results can differ by about ±0.01 between computers.

## Model Details
- **Person or organization developing model:** [Anonymized for review.]
- **Model date:** 2026
- **Model version:** 1.0 (as in this repository)
- **Model type:** Gradient-boosted decision trees (XGBoost `XGBClassifier`) for binary classification, with isotonic regression calibration. A logistic regression model (median imputation, standardization, `class_weight="balanced"`), climatology, and a wind-trend rule are trained as baselines.
- **Training approach and features:**
  - 27 features from IBTrACS (current storm state and wind changes over the past 6, 12, and 24 hours), ERA5 (sea surface temperature, sea level pressure, water vapor, upper and lower level winds, vertical wind shear), and GridSat-B1 (cold-cloud fractions). See `datasheet/DATASHEET.md`.
  - The feature set and model were chosen by 5-fold cross-validation on the training years, grouped by storm, ranked by PR-AUC. The number of trees (225) is the mean early-stopping iteration across folds.
  - Class imbalance is handled with `scale_pos_weight` = number of non-RI cases / number of RI cases in the training data (13.2).
  - Probabilities are calibrated with isotonic regression on the validation years.
- **Paper or other resource for more information:** `notebook/tropicalcyclone_rapidintensification.ipynb`
- **Citation details:** See the Cite section of the README.
- **License:** MIT
- **Where to send questions or comments about the model:** GitHub issues on this repository.

## Intended Use
- **Primary intended uses:** Teaching and research: learning how to build, evaluate, and interpret an ML model for a rare, high-impact climate-related event.
- **Primary intended users:** Students, ML practitioners, and climate researchers.
- **Out-of-scope use cases:**
  - Real-time forecasting or warnings. The model is trained on data that are available only after a storm (ERA5 reanalysis, final best tracks, GridSat-B1).
  - Storms over land, close to landfall, or becoming extratropical, which are excluded from the training labels.
  - Any safety-critical decision. Official forecasts, such as those of the National Hurricane Center, should always be used.

## Factors
- **Relevant factors:** Ocean basin, storm strength (Saffir-Simpson category), year, and whether RI is starting or already under way.
- **Evaluation factors:** The notebook reports results by basin, by test year, and by storm strength, and compares RI onsets with ongoing RI.

## Metrics
- **Model performance measures:**
  - *Primary:* PR-AUC (average precision). Its no-skill value is the RI rate.
  - *Supporting:* Brier skill score (BSS) against training climatology, and probability of detection (POD) and false alarm ratio (FAR) at the decision threshold.
- **Decision thresholds:** 0.131, the threshold with the highest F2 score on the validation years. F2 weights missing an RI event more heavily than a false alarm. F2 stays within 0.01 of its best value for thresholds from about 0.09 to 0.15.
- **Variation approaches:** Cross-validation scores are reported as mean ± standard deviation across 5 storm-grouped folds. A leave-one-feature-group-out ablation reports in how many folds PR-AUC drops.

## Evaluation Data
- **Datasets:** Test seasons 2016–2025: 13,798 observations from 859 storms, RI rate 10.2%. Validation seasons 2011–2015 (7,496 observations, RI rate 9.9%) are used for calibration and threshold selection.
- **Motivation:** A chronological split tests the model on future storms, as in real use, and includes the recent rise in RI rate.
- **Preprocessing:** Same as training (see `datasheet/DATASHEET.md`).

## Training Data
Seasons 1980–2010: 57,178 observations from 2,816 storms, RI rate 7.0%. No storm appears in more than one split.

## Quantitative Analyses
- **Unitary results (test years 2016–2025):**

| Model | PR-AUC (no skill 0.102) | BSS |
|---|---|---|
| Climatology | 0.102 | 0.00 |
| Wind-trend rule (yes/no) | 0.179 | n/a |
| Logistic regression (calibrated) | 0.325 | +0.16 |
| **XGBoost (calibrated)** | **0.421** | **+0.23** |

  At the 0.131 threshold, XGBoost raises an alarm for 25.6% of test observations, with POD = 0.75 and FAR = 0.70 (precision 0.30, about three times the RI rate). Validation results are similar (POD 0.78, FAR 0.70). On the test years, the calibrated probabilities stay close to the observed frequencies (mean predicted 0.095 vs observed 0.102).

- **Intersectional results (test years):**
  - *By basin:* PR-AUC ranges from 0.32 (South Pacific) to 0.52 (North Indian), 3.2 to 5.0 times each basin's RI rate. Basins with few storms have less certain results.
  - *By storm strength:* best for tropical storms and Category 1–2 hurricanes (PR-AUC 0.42–0.53), weak for tropical depressions and Category 4 hurricanes (PR-AUC about 0.13–0.14), where RI is rare.
  - *By RI stage:* catches 55% of RI onsets and 83% of ongoing RI cases.
  - *By year:* PR-AUC between 0.32 and 0.50, 3.6 to 4.8 times each year's RI rate, with no downward trend.

## Ethical Considerations
- Missed RI events can lead to under-prepared communities, and frequent false alarms can reduce trust in warnings. This model is a teaching tool and must not replace official forecasts.
- Performance varies by basin. Regions with fewer storms or poorer historical observations may be less well served.
- The training data come from public scientific archives and contain no personal information.

## Caveats and Recommendations
- **Distribution shift:** RI is more common in recent years (7.0% in training vs 10.2% in test years), and future climates may differ further. Re-evaluate on recent data before use.
- **Relies on recent strengthening:** the strongest signal is the wind change over the past 6–12 hours, so the start of RI is harder to catch than ongoing RI.
- **Many false alarms at the F2 threshold:** about half of them still strengthened by at least 15 kt. A higher threshold gives fewer false alarms but misses more RI events.
- **Simple features:** area averages around the storm leave out the storm's inner structure.
- **Not real-time:** retraining and testing on real-time inputs (operational analyses, working best tracks, live satellite images) would be needed. See the Limitations section of the notebook.
