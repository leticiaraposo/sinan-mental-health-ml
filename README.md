# Machine Learning Prediction of Work-Related Sick Leave in Mental Disorders

Code and analysis pipeline for the study *"Machine Learning Prediction of
Work-Related Sick Leave in Mental Disorders: A TRIPOD+AI Analysis of
Brazilian National Surveillance Data (2006–2025)"*.

[![DOI](https://zenodo.org/badge/1295588381.svg)](https://doi.org/10.5281/zenodo.21283514)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![R](https://img.shields.io/badge/R-4.4.1-blue.svg)](https://www.r-project.org/)


## Overview

We used 20 years of records from Brazil's national mandatory disease
notification system (SINAN, condition code F99 — work-related mental
disorder) to develop and internally validate three machine learning models
predicting whether a notified worker would be granted workplace absence
(*afastamento*). Beyond discrimination, we assessed calibration, clinical
utility (decision curve analysis), and — the central contribution of the
study — quantified how model performance varies across sex, race/ethnicity,
educational attainment, and geographic region, following the [TRIPOD+AI
statement](https://doi.org/10.1136/bmj-2023-078378).

**Sample:** n = 21,601 notifications (2006–2025), after excluding records
with a missing outcome.
**Champion model:** Random Forest (selected by test-set AUC-PR; statistically
indistinguishable from XGBoost, both significantly better than Elastic Net —
see `output/tables/table_pairwise_model_comparison.xlsx`).

| Model | AUC-ROC (95% CI) | AUC-PR (95% CI) |
|---|---|---|
| Elastic Net | 0.679 (0.664–0.694) | 0.778 (0.761–0.793) |
| Random Forest | 0.696 (0.682–0.710) | 0.801 (0.787–0.815) |
| XGBoost | 0.699 (0.685–0.713) | 0.800 (0.785–0.814) |

Discrimination varied substantially across subgroups (e.g., test-set
AUC-ROC of 0.613 for Black notifiers vs. 0.696 overall) — see
`output/figures/fig6_fairness.png` and `output/tables/table3_fairness.xlsx`.
We do **not** consider the models in this repository ready for individual-level
deployment; see the manuscript's Discussion and Limitations for details.

## Repository structure
.
├── pipeline_mental_health.R # full analysis pipeline (single script)
├── data/
│ └── raw/ # input data (not tracked in Git — see below)
├── output/
│ ├── figures/ # all manuscript and supplementary figures
│ ├── tables/ # all manuscript and supplementary tables
│ └── models/ # fitted model objects (not tracked in Git)
├── renv.lock # pinned package versions (if present)
├── CITATION.cff
└── README.md

## Data
The raw notification-level data (`sinan_mental_health_2006_2025.rds`) are
not stored in this Git repository. They are derived from SINAN, Brazil's
national mandatory disease-notification system, publicly accessible through
[DATASUS](https://datasus.saude.gov.br) and the
[SINAN public data portal](https://portalsinan.saude.gov.br). A
de-identified, analysis-ready extract is archived separately on Zenodo
(DOI: *to be added*) — see **Data and code availability** below.

## Reproducing the analysis
Requirements: R ≥ 4.4.1.
```r
# 1. Clone the repository
git clone https://github.com/<your-username>/sinan-mental-health-ml.git
cd sinan-mental-health-ml
# 2. Restore the exact package versions used in the study (if renv.lock is present)
install.packages("renv")
renv::restore()
# 3. Place the input data at data/raw/sinan_mental_health_2006_2025.rds
#    (download from the Zenodo data deposit — see below)
# 4. Run the full pipeline from the repository root
source("pipeline_mental_health.R")
```

The script creates output/figures/, output/tables/, and
output/models/ and populates them with every figure, table, and fitted
model object referenced in the manuscript and its supplementary material.
Runtime is dominated by hyperparameter tuning (Section 07).

## Methods summary
Framework: tidymodels
Models: Elastic Net logistic regression (glmnet), Random Forest
(ranger), XGBoost (xgboost), tuned by 5-fold cross-validation
Class imbalance: random minority-class oversampling (training folds
only)
Interpretability: permutation importance and SHAP (fastshap,
shapviz) for the champion model
Fairness: subgroup AUC-ROC by sex, race/ethnicity, education, and
region, with a pre-specified 0.05 disparity threshold
Reporting: TRIPOD+AI
(completed checklist in the manuscript's Supplementary Material)

## Citation
If you use this code or data, please cite the associated article (citation
to be added upon publication) and, if referring specifically to the code or
data, the Zenodo DOIs above. 

## Authors
Beatriz Queiroz Reis — ORCID 0009-0003-1645-0225
Letícia Martins Raposo — ORCID 0000-0003-0613-5582
Centro de Ciências Exatas e Tecnologia, Universidade Federal do Estado do
Rio de Janeiro (UNIRIO)

## License
Code released under the MIT License. See the Zenodo data deposit
for the data license/access terms.
Code released under the MIT License. See the Zenodo data deposit
for the data license/access terms.
