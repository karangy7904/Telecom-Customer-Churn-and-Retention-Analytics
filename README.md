# Telecom Customer Churn and Retention Analytics

A complete customer churn case study that connects predictive modeling to a capacity-constrained retention decision. The project uses 7,043 customer records to investigate churn patterns, compare classifiers, explain model behavior, segment customers, and simulate campaign economics.

## Key results

| Held-out test metric | Result |
| --- | ---: |
| Selected model | Random forest with sigmoid calibration |
| Average precision | 0.654 |
| ROC-AUC | 0.842 |
| Churners captured in the highest-risk 10% | 27.3% |
| Top-10% lift over random targeting | 2.73× |

These results come from the saved notebook outputs. The dataset has no observation dates, so this is retrospective classification rather than a validated future-window forecast.

![Held-out model evaluation](images/model-evaluation.png)

## Workflow

1. **Business framing:** define the retention objective, contact capacity, and evaluation criteria.
2. **Data cleaning:** validate customer IDs, labels, numeric fields, duplicates, and missing charges.
3. **EDA:** explore tenure, pricing, contract, service, and payment associations with churn.
4. **Hypothesis testing:** use chi-square and Welch tests with effect sizes and Holm adjustment.
5. **Feature engineering:** derive tenure and service features within reproducible preprocessing pipelines.
6. **Model comparison:** compare a prevalence baseline, logistic regression, random forest, and histogram gradient boosting.
7. **Model explainability:** use validation permutation importance and a separate logistic reference explanation.
8. **Customer segmentation:** create three behavioral segments using training-fitted K-means.
9. **Retention strategy:** compare random, risk-based, and expected-net-value targeting under a 10% capacity limit.
10. **Business-impact simulation:** evaluate campaign economics, sensitivity, break-even assumptions, and 5,000 simulated outcomes.

## Run in Google Colab

1. Download `Telco_Churn_Retention.ipynb` and the CSV in `data/` from this repository.
2. Open Google Colab and upload the notebook.
3. Choose **Runtime → Run all**.
4. When section 2 shows the file picker, upload `WA_Fn-UseC_-Telco-Customer-Churn.csv`.
5. Wait for the remaining cells to finish. A fresh runtime requires another CSV upload.

The notebook deliberately uses Colab's upload picker. It does not automatically read the repository's data folder, and its upload cell requires Colab. `requirements.txt` records the six analytical package versions displayed in the supplied run. Colab supplies the notebook runtime and its upload API. If matching the recorded package environment, install the requirements in a setup cell with `%pip install -r requirements.txt` after uploading that file, then restart the runtime before running the analysis.

## Methodology

- Stratified 60% training, 20% validation, and 20% test split with random seed 42.
- EDA and exploratory tests use training data only.
- Imputation, scaling, encoding, and feature engineering are fitted within model pipelines.
- Three-fold training cross-validation selects the model by average precision.
- Training-only cross-validation calibrates probabilities; validation selects the F1 threshold.
- The fixed model is evaluated on held-out test data. Campaign rankings are separate from the classification threshold.
- Identifiers and demographic fields are excluded as model inputs; this alone does not establish fairness.

## Simulated business impact

Base assumptions are **5 currency units (CU) contact cost**, **20 CU offer cost per contact**, **20% incremental save probability among otherwise-churning customers**, **six additional retained months**, and **40% contribution margin**.

Expected net contribution is calculated as:

```text
sum over contacted customers:
    churn probability × incremental save probability
    × monthly charges × retained months × contribution margin
    − contact cost − offer cost
```

Under these assumptions, the value policy selects 141 test-cohort customers and produces approximately **1,097 CU of expected incremental net contribution**. Its break-even save probability is approximately **15.3%**. These are hypothetical scenario outputs, not measured savings. The simulation draws uncertain campaign effectiveness and individual churn/save outcomes; its percentile ranges are scenario ranges, not confidence intervals.

![Monte Carlo campaign outcomes](images/impact-simulation.png)

## Repository contents

```text
Telco_Churn_Retention.ipynb    Executed Colab notebook with charts and results
README.md                    Project overview and run instructions
requirements.txt             Recorded analytical package versions
.gitignore                   Excludes local caches and environment files
data/
  WA_Fn-UseC_-Telco-Customer-Churn.csv
images/
  model-evaluation.png
  impact-simulation.png
```

## Data and reproducibility

The CSV is the user-supplied Telco customer churn file, originally named `WA_Fn-UseC_-Telco-Customer-Churn (1).csv`. It contains 7,043 rows and 21 original columns. Eleven total-charge values are missing and are handled within the preprocessing pipelines. No additional data is introduced.

Source SHA-256: `88be4b93fbe0cc83421af1c503794c97c342eca914c1576db7c276e61d61358a`.

The supplied executed notebook records Python 3.13.15, execution counts 1 through 14, and no saved error outputs. The notebook is preserved unchanged; packaging checks verified its syntax, recorded completion, and dataset fingerprint. This packaging step did not rerun the analysis. Introductory validation notes inside the notebook describe its earlier preparation; the attached saved run provides the newer execution evidence.

## Limitations and next steps

The dataset contains no treatment outcomes, dated feature snapshots, or verified future prediction window. Associations and risk scores do not measure the causal effect of a retention offer. A production extension would establish pre-outcome feature availability, evaluate a later time period, audit subgroup outcomes and calibration, and run a randomized retention pilot before claiming business savings.
