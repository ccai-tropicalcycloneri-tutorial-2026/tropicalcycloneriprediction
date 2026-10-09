# Tropical Cyclone Rapid Intensification (RI) Prediction using Machine Learning

![Python](https://img.shields.io/badge/language-Python%203.13-blue)
![Jupyter Notebook](https://img.shields.io/badge/Jupyter-Notebook-orange)
![XGBoost](https://img.shields.io/badge/XGBoost-3.4-red)
![scikit-learn](https://img.shields.io/badge/scikit--learn-1.9-f7931e)
![MIT License](https://img.shields.io/badge/license-MIT-green)

## Abstract
Rapid intensification (RI), an increase in a tropical cyclone's maximum wind of at least 30 knots in 24 hours, is one of the hardest problems in operational forecasting, and it can turn a moderate storm into a major hurricane shortly before landfall. In this beginner-friendly tutorial, we build a multi-modal dataset from IBTrACS best tracks, ERA5 reanalysis, and GridSat-B1 satellite imagery, and train logistic regression and XGBoost models to predict whether each 6-hourly storm observation will undergo RI in the next 24 hours. Along the way, we cover physically motivated features, storm-grouped and chronological data splits, class imbalance, probability calibration, and metrics for rare events (PR-AUC, Brier skill score, POD, and FAR). We close with an honest error analysis, a case study of Hurricane Milton (2024), and a discussion of what it would take to move from a notebook to operational RI forecasting.

### Authors:

- **[Anonymized for review]**

> Reminder for Round II reviews please keep all tutorial materials anonymized to support the double-blind review process.

Originally presented at the [Tackling Climate Change with Machine Learning workshop at NeurIPS 2026].

## Learning Objectives
After completing this tutorial, participants should be able to:
1. Explain what RI is and why it is difficult to forecast.
2. Derive and understand explainable features from a multi-modal dataset of best-track records (IBTrACS), ERA5 reanalysis, and satellite imagery (GridSat-B1).
3. Visualize and explore the properties and behavior of tropical cyclones.
4. Recognize why class imbalance makes standard metrics like accuracy misleading for rare-event problems.
5. Choose and interpret evaluation metrics suited to rare, high-stakes prediction problems: precision-recall curves, probability of detection, false alarm ratio, and Brier skill score.
6. Critically assess an ML model's real-world limitations.

### Intended Audience
- Beginner-to-intermediate ML practitioners and students with basic Python knowledge, looking for a hands-on example of applying ML to a real-world, climate-relevant forecasting problem. No prior meteorology background is required.
- Climate scientists and researchers interested in how ML can be applied to a forecasting problem they already understand.
- Anyone working, or hoping to work, at the intersection of ML and climate science.

### Prerequisites
Participants should be familiar with:
- Basic Python, including `pandas` and `numpy`.
- Basic ML concepts: train/validation/test splits, binary classification, and some familiarity with `scikit-learn`.
- Running Jupyter notebooks (for example, in Google Colab).

### Tutorial walkthrough video

➡️ [**Video URL**](url)
**Duration:** [XX minutes]

---

## Repository Contents

| Resource | Description |
|----------|-------------|
| `notebook/` | Jupyter notebook |
| `model-card/` | Model documentation |
| `datasheet/` | Dataset documentation |
| `emissions-reporting/` | Energy/carbon reporting |
| `figures/` | Diagrams used in the notebook |

The precomputed ERA5 and satellite feature files are published as a [GitHub release](https://github.com/ccai-tropicalcycloneri-tutorial-2026/tropicalcycloneriprediction/releases/tag/dataset_v4) and are downloaded by the notebook.

---

## Software Requirements

**Primary language:** Python

**Language version:** Python 3.13

### Major Packages
- xgboost 3.4.1
- scikit-learn 1.9.0
- pandas 2.2.3
- xarray 2026.7.0
- codecarbon 3.3.1

See `requirements.txt` for the complete environment.

---

## Access this tutorial
We recommend executing this notebook in a Colab environment to manage all necessary dependencies. <a target="_blank" href="https://colab.research.google.com/github/ccai-tropicalcycloneri-tutorial-2026/tropicalcycloneriprediction/blob/main/notebook/tropicalcyclone_rapidintensification.ipynb">
  <img src="https://colab.research.google.com/assets/colab-badge.svg" alt="Open In Colab"/>
</a>

To run locally, see:

➡️ `notebook/tropicalcyclone_rapidintensification.ipynb`

**Estimated time to execute end-to-end:** about 2 hours.

**Last successfully tested:** [YYYY-MM-DD]

### Model Information
This submission involves a machine-learning model (logistic regression and XGBoost classifiers for 24-hour RI prediction).

See:

➡️ `model-card/MODEL_CARD.md`

### Dataset Information
This submission uses a dataset built from IBTrACS, ERA5, and GridSat-B1.

See:

➡️ `datasheet/DATASHEET.md`

### Sustainability/Carbon Emissions
Carbon emissions associated with the computational experiments were measured using [CodeCarbon](https://codecarbon.io/).

For more details, go to:

➡️ `emissions-reporting/CARBON_EMISSIONS.md`

---

## Contribute to this tutorial

Please refer to these [GitHub instructions](https://docs.github.com/en/get-started/exploring-projects-on-github/contributing-to-a-project#about-forking) to open a pull request via the "fork and pull request" workflow.

Pull requests will be reviewed by members of the Climate Change AI Tutorials team for relevance, accuracy, and conciseness.

## Climate Change AI Tutorials
Check out the [tutorials page](https://www.climatechange.ai/tutorials?) on our website for a full list of tutorials demonstrating how AI can be used to tackle problems related to climate change.

## License
Usage of this tutorial is subject to the MIT License.

## Cite

### Plain Text
[Insert plain text citation of accepted submission here.]

### BibTeX
[Insert BibTex citation of accepted submission here.]
