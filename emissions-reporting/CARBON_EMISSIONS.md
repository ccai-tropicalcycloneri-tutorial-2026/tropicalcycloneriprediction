# Carbon Emissions and Computational Sustainability

> Fields marked **[TO FILL after Colab run]** come from the CodeCarbon output of the final Colab run (`emissions.csv`, which can be added to this folder).

---

## 1. Computation included

### What computation did you perform?

- [x] Data preprocessing
- [x] Model training
- [ ] Model fine-tuning
- [x] Hyperparameter tuning
- [x] Model evaluation or benchmarking
- [x] Model inference
- [x] Complete notebook execution
- [x] Other: Precomputing ERA5 and GridSat-B1 features for all observations (done once, before the tutorial)

### What is included in the energy and emissions reported below?

- [ ] Data preprocessing
- [x] Model training
- [ ] Model fine-tuning
- [x] Hyperparameter tuning
- [ ] Model evaluation or benchmarking
- [ ] Model inference
- [ ] Complete notebook execution
- [ ] Other

**Briefly explain what was tracked:**

CodeCarbon tracked the two training steps in the notebook: 5-fold cross-validation over 9 feature sets × 2 models (which also selects the number of trees through early stopping), and the final refit of the best model on the training years.

### What is not included?

Downloading and preprocessing the data in the notebook, calibration, evaluation, and plotting. The one-time precomputation of the ERA5 and GridSat-B1 features (published in the `dataset_v4` release) and early development runs were not tracked.

---

## 2. Hardware and computing environment
**Computing environment:**

- [ ] Personal computer or workstation
- [ ] Institutional server
- [ ] Computing cluster
- [ ] Cloud system
- [x] Google Colab
- [ ] Other

**CPU:**
[TO FILL after Colab run: from `emissions.csv`, column `cpu_model`]

**GPU or other accelerator:**
None (the models train on CPU)

**RAM:**
[TO FILL after Colab run: column `ram_total_size`]

**Computing provider:**
Google Colab

**Computing location or cloud region:**
[TO FILL after Colab run: columns `country_name` / `region`]

---

## 3. Operational carbon emissions (Required)

### How were emissions tracked?

- [x] CodeCarbon
- [ ] Another tracking tool
- [ ] Cloud or system information
- [ ] Not measured
- [ ] Not applicable

**Tool:**
CodeCarbon

**Tool version:**
3.3.1

**Number of runs included:**
1 (one cross-validation run and one refit, from the final notebook run)

**Total computing time:**
[TO FILL after Colab run: sum of the `duration` column, in hours]

**Energy used:**
[TO FILL after Colab run: sum of the `energy_consumed` column, in kWh]

**Carbon emissions:**
[TO FILL after Colab run: cross-validation + refit, in kg CO2e, as printed in the notebook]

**Source used to determine the carbon emissions of the electricity:**
CodeCarbon (carbon intensity of the electricity grid at the detected location)

---

## 4. Embodied Emissions (Report if possible)

**Were these emissions estimated?**

- [ ] Yes
- [x] No
- [ ] Not applicable

---

## 5. Idle Consumption emissions: Water use (Report if possible)

**Was water use estimated?**

- [ ] Yes
- [x] No
- [ ] Not applicable

---

## 6. Reducing computational impact (Required)

- [ ] Reused a pretrained model
- [x] Used a simpler or smaller model
- [x] Used early stopping
- [x] Reduced hyperparameter trials
- [ ] Used a more efficient hyperparameter search
- [x] Reduced repeated experiments
- [x] Reused cached or previously computed results
- [ ] Used more efficient hardware
- [ ] Used a lower-carbon computing location or time
- [ ] Reduced unnecessary inference
- [ ] Other
- [ ] No specific reduction strategy was used

**Briefly explain the most important action taken:**

The ERA5 and GridSat-B1 features are computed once and shared as small Parquet files, so learners do not need to download and process large gridded datasets on every run. The models are small tabular models (logistic regression and XGBoost) that train on a CPU, with fixed hyperparameters chosen in advance and early stopping to set the number of trees, instead of a large hyperparameter search.

---

## 7. Effect on model performance

- [ ] Yes
- [ ] No meaningful difference observed
- [x] Not evaluated
- [ ] Not applicable

**What changed?**

We did not compare against larger models or larger hyperparameter searches. The notebook shows that the simple XGBoost model reaches a test PR-AUC about four times the no-skill level.

---

## 8. Limitations (Required)

- [x] Some experimental runs were not tracked
- [x] Hardware energy use was estimated
- [ ] Exact computing location was unknown
- [x] Electricity carbon emissions were estimated
- [x] Data-center energy use was not fully included
- [x] Hardware-production emissions were not estimated
- [x] Water use was not estimated
- [ ] Remote AI-service infrastructure could not be measured
- [x] Other: The one-time feature precomputation and data downloads were not tracked.
