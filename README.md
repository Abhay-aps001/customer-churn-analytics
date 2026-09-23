# Customer Retention Intelligence — Telecom Customer Churn Analytics and Prediction

## Overview

An IBM Data Analytics internship project that turns telecom customer data into descriptive insights, an interpretable churn classifier, and recommendations for retention pilots. The executed Jupyter Notebook is the main analysis file and the source of all results reported here.

## Problem Statement

Customer churn reduces the customer base and creates a need for replacement sales. This project examines which customer groups have higher observed churn and whether a simple classifier can help prioritize further investigation and retention outreach.

## Business Objectives

- Summarize customer scale, churn, charges, and tenure through clear KPIs.
- Identify customer segments associated with elevated observed churn.
- Evaluate Logistic Regression against a majority-class baseline.
- Rank held-out customers by predicted churn probability.
- Propose measurable retention actions for controlled testing.

## Dataset

- **Source:** [Telco Customer Churn on Kaggle](https://www.kaggle.com/datasets/blastchar/telco-customer-churn).
- **Local file:** `data/WA_Fn-UseC_-Telco-Customer-Churn.csv`.
- **Size:** 7,043 customer records and 21 original columns.
- **Target:** `Churn`, with `Yes` treated as the positive class.
- **Features:** customer demographics, tenure, service subscriptions, contracts, payment methods, and charges.

The dataset is attributed to the linked source; the project does not claim ownership. Currency is unspecified throughout the analysis.

Cleaning converts `TotalCharges` to numeric and fills its 11 blank values with zero only for records with `tenure = 0`. This is an explicit assumption that accumulated charges have not yet been recorded. All customer records are retained. Meaningful categories such as `No internet service` remain separate categories.

## Project Workflow

Data Understanding → Data Cleaning → Exploratory Data Analysis → KPI Analysis → Churn Driver Analysis → Logistic Regression → Model Evaluation → Risk Analysis → Business Recommendations

The notebook contains eight visualizations, including segment comparisons, a confusion matrix, an ROC curve, and Logistic Regression coefficients.

## Key KPIs

| KPI | Value | Population |
| --- | ---: | --- |
| Total Customers | 7,043 | Full dataset |
| Observed Churn Rate | 26.54% | Full dataset |
| Average Monthly Charges | 64.76 | Full dataset; currency unspecified |
| Average Tenure | 32.37 months | Full dataset |
| High-Risk Customers in Held-Out Test Set | 317 of 1,409 | Held-out test set only |

**317 of 1,409 held-out customers were classified as high risk at a predicted churn probability threshold of 0.50.** High risk includes probabilities equal to 0.50. This is not a count of high-risk customers among all 7,043 records and is not extrapolated to the full dataset.

## Key Findings

| Customer segment | Observed churn rate |
| --- | ---: |
| Month-to-month contracts | 42.71% |
| Tenure 0–12 months | 47.44% |
| Electronic check and month-to-month contract | 53.73% |

These are observed associations, not proof of causation. Segments can overlap, and their differences may reflect other customer characteristics. Contract type or payment method is not established as a cause of churn.

## Model

Logistic Regression was selected because churn is a binary classification problem and the model is interpretable. Its coefficients help explain conditional associations with churn, while keeping the project focused on business interpretability rather than unnecessary complexity.

- **Split:** 80% training and 20% held-out testing, stratified by churn, with `random_state = 42`.
- **Preprocessing:** a scikit-learn pipeline standardizes numerical features and one-hot encodes categorical features. Preprocessing is fitted on training data only.
- **Predictors:** `customerID`, the target, and target-derived fields are excluded. Original numerical tenure is used instead of the exploratory tenure bands.
- **Classifier:** regularized Logistic Regression with `C = 1.0`, the `lbfgs` solver, and a maximum of 2,000 iterations.
- **Decision rule:** classify churn when predicted probability is **≥ 0.50**.
- **Interpretation:** numerical coefficients describe a one-training-standard-deviation change; categorical coefficients compare with the reference categories displayed in the notebook. Coefficients are not causal effects.

## Model Performance

Results are measured on the **1,409-customer held-out test set**, with `Churn = Yes` as the positive class. The test set was not used to tune model parameters or the threshold.

| Metric | Logistic Regression |
| --- | ---: |
| Accuracy | 80.62% |
| Precision | 65.93% |
| Recall | 55.88% |
| F1-score | 0.6049 |
| ROC-AUC | 0.8422 |

The majority-class baseline, which always predicts the training majority class, achieves **73.46% accuracy** on the same test set. Logistic Regression improves on that baseline, but its moderate recall means a substantial number of churners are still missed. ROC-AUC measures ranking discrimination, not the effectiveness of a retention campaign.

## Confusion Matrix

Rows represent actual outcomes; columns represent predictions at the 0.50 threshold.

| Actual outcome | Predicted no churn | Predicted churn |
| --- | ---: | ---: |
| No churn | TN = 927 | FP = 108 |
| Churn | FN = 165 | TP = 209 |

- **False negatives:** 165 actual churners were classified as non-churners, representing missed opportunities for investigation.
- **False positives:** 108 non-churners were flagged, potentially leading to unnecessary retention contact.

## Business Recommendations

Treat these as hypotheses for **controlled retention pilots**, not guaranteed interventions:

1. **Early-tenure onboarding:** test service check-ins and setup support, measuring retention against a comparison group over a predefined follow-up period.
2. **Optional contract offers:** test suitable offers for month-to-month customers; measure incremental retention, uptake, and incentive cost.
3. **Service and support investigation:** examine fiber-service experience and technical-support access using complaint and service-quality data before selecting an intervention.
4. **Billing assistance:** investigate billing friction and explain available payment options; evaluate complaints, failed payments, and retention in a pilot.
5. **Risk-prioritized outreach:** validate probability-ranked outreach on current eligible customers and monitor retained customers, contact costs, opt-outs, and outcomes across customer groups.

The notebook's held-out ranking is a retrospective demonstration, not a live outreach list. The supplied records already contain churn outcomes; already-churned customers should not be treated as current retention prospects. No financial savings or return on investment is claimed.

## Technologies Used

- **Python 3.12** for the analysis.
- **Jupyter Notebook, IPython, and ipykernel** for interactive execution and rich outputs.
- **pandas and NumPy** for data preparation and calculations.
- **Matplotlib and seaborn** for visualization.
- **scikit-learn** for preprocessing, classification, and evaluation.

The notebook displays the analysis package versions used for its submitted execution. `requirements.txt` pins the imported libraries, Python kernel, and Jupyter Notebook interface. Dependency compatibility was checked with pip on Python 3.12.

## Repository Structure

```text
customer-churn-analytics/
├── AbhayPratapSingh_CustomerChurnAnalytics.ipynb
├── requirements.txt
├── README.md
├── AbhayPratapSingh_ProjectReport.docx
├── data/
│   └── WA_Fn-UseC_-Telco-Customer-Churn.csv
└── images/
```

`AbhayPratapSingh_ProjectReport.docx` is an intended final submission artifact and has not been created yet. The notebook embeds its figures; the `images` directory is currently reserved for project images.

## Setup and Installation

Install Git and Python 3.12 with the Windows Python launcher. In PowerShell, run:

```powershell
git clone https://github.com/Abhay-aps001/customer-churn-analytics.git
cd customer-churn-analytics
py -3.12 -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install -r requirements.txt
jupyter notebook
```

Open **`AbhayPratapSingh_CustomerChurnAnalytics.ipynb`**, select the environment's Python kernel, and restart the kernel and run all cells from top to bottom. Start Jupyter from the repository root so the relative dataset path resolves correctly. The CSV is included in the repository.

If PowerShell blocks activation, use the environment's executables directly after creating it:

```powershell
.\.venv\Scripts\python.exe -m pip install -r requirements.txt
.\.venv\Scripts\jupyter.exe notebook
```

## Limitations

- Observational associations and model coefficients are not causal effects.
- No temporal prediction horizon or dated follow-up exists in the supplied snapshot; this is not a validated future-churn forecast.
- Logistic Regression may miss nonlinear relationships, and correlated features complicate individual coefficient interpretation.
- Recall is moderate, so some churners are missed at the specified threshold.
- Model performance is based on a single held-out split, without external or temporal validation.
- Missing total charges at zero tenure are handled using a documented assumption.
- Predicted probabilities have not been assessed for calibration, and the threshold is not optimized for campaign costs.
- Currency, campaign costs, margins, and intervention outcomes are unavailable. Operational use would require further validation and fairness review.

## Author

Abhay Pratap Singh
