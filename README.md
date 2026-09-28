# Early-Warning Screening of Unprofitable Health-Insurance Policies

Group project, SMU MITB coursework (2026):

**Question.** Which existing health-insurance policies are likely to become unprofitable (annual claim cost above annual premium), and can a model rank them well enough to focus underwriting review?

## Data

Public Spanish health-insurance portfolio, 2017–2019: 228,711 insured-person-year records, 42 variables (policy, demographic, geographic, socioeconomic and climate). Unprofitable policies are a minority: 17.8% in the 2018 validation set, 15.5% in the 2019 test set.

## Method

- **Strict temporal protocol:** 2017 used only to build prior-year features; 2018 split into development and validation for model and threshold selection; 2019 held out and scored once, after everything was fixed.
- **Leakage control:** current-year claim cost, medical-service count and exposure time excluded, since none is known at scoring time.
- **Features:** policyholder variables, K-Means segment (k = 3) and prior-year lags (premium, claim cost, claim ratio, prior unprofitability, premium change).
- **Models:** logistic regression, decision tree, random forest, XGBoost, LightGBM.

## Results

All tree models plateaued at ROC-AUC 0.72–0.74, so direct yes/no classification was not reliable. The task was reframed as **risk ranking**. LightGBM was selected for its ranking quality, and a threshold fixed at the 2018 top-30% boundary was then applied once to 2019:

| | 2018 validation (design) | 2019 test (fixed threshold) |
|---|---|---|
| Policies flagged | 30.0% | 27.9% |
| Unprofitable policies captured | 58.4% | 54.3% |
| Precision | 34.6% | 30.2% |
| Lift | 1.94× | 1.95× |
| ROC-AUC | 0.741 | 0.727 |

Lift stayed stable out of time, so reviewing the flagged 28% of the portfolio would catch just over half of the losses, roughly twice as efficient as random review.

## Limitations

No clinical detail (diagnoses, severity), which caps accuracy; class weighting means scores are rankings, not calibrated probabilities; review volume changes with the score distribution; the cost of a review and the value of an intervention are not yet quantified.

## Repository

`docs/report.pdf`: full technical report (pipeline, clustering, model comparison, decile/lift analysis, proposed human-in-the-loop review workflow). The code is not included in this repository.

Tools: Python, scikit-learn, XGBoost, LightGBM, pandas.
