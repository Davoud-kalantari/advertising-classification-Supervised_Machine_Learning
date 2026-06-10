# Advertising Click-Through Prediction with Statistical Learning

## Executive Summary

This repository presents a complete classification workflow for predicting whether a user will click on an advertisement. The project combines exploratory data analysis, model benchmarking, cross-validated evaluation, and final test-set prediction export in a reproducible R workflow.

The main objective is not just prediction, but model comparison: the analysis contrasts interpretable statistical classifiers with tree-based and ensemble methods to identify the strongest balance between accuracy, stability, and explainability.

## Business Problem

Marketing teams need a reliable way to estimate click-through behavior before deploying campaigns at scale. A strong classifier helps:

- prioritize higher-probability leads
- evaluate campaign performance earlier in the funnel
- support budget allocation and targeting decisions
- keep the modeling process interpretable enough for operational use

The target variable is binary (`Clicked.on.Ad`), making this a standard statistical learning classification problem.

## Dataset Overview

The project uses a structured advertising dataset with a train/test split already provided in the repository.

### Inputs

- `data/advertising_train.csv`
- `data/advertising_test.csv`
- `data/advertisingdatanote`

### Variables

The dataset includes behavioral, demographic, and campaign-related predictors such as:

- time spent on site
- age
- area income
- internet usage
- ad topic
- city
- gender
- country
- timestamp

The cleaned modeling frame also removes the index field and converts the timestamp and categorical variables into model-friendly formats.

## Methodology

The analysis follows a standard statistical learning pipeline:

1. Data loading from the project-relative `data/` folder.
2. Cleaning and feature preparation.
3. Exploratory analysis of distributions, missingness, and correlations.
4. Model fitting on the training set.
5. Stratified 10-fold cross-validation using the same folds across models.
6. Model ranking using cross-validated accuracy and misclassification error.
7. ROC and AUC analysis as a secondary diagnostic.
8. Final refit on the full training data and prediction export for the test set.

This workflow is designed to make the comparison fair, reproducible, and easy to audit.

## Models Evaluated

The repository compares a mix of linear, tree-based, and ensemble classifiers because they offer different tradeoffs in bias, variance, and interpretability.

| Model | Why it was included |
|---|---|
| Logistic Regression | Strong interpretable baseline and a natural choice for binary classification. |
| LDA | Classic linear classifier with a probabilistic formulation and strong statistical learning pedigree. |
| QDA | More flexible than LDA; tests whether relaxing the equal-covariance assumption improves performance. |
| Classification Tree | Highly interpretable rule-based model that captures nonlinear splits. |
| Pruned Tree | Complexity-controlled tree built to reduce overfitting and improve generalization. |
| Bagging | Variance-reduction benchmark for tree ensembles. |
| Random Forest | Decorrelated ensemble benchmark that often improves predictive stability. |
| Boosting | Sequential ensemble benchmark used to test whether shallow learners can outperform simpler methods. |

These models were compared to understand whether a simple interpretable classifier was sufficient or whether more flexible methods produced a meaningful gain.

## Results and Model Comparison

Model comparison was based primarily on cross-validated accuracy and misclassification error, with AUC used as a secondary diagnostic.

| Model | Cross-Validated Accuracy | Misclassification Error | AUC |
|---|---:|---:|---:|
| Logistic Regression | 96.75% | 3.25% | 0.990 |
| QDA | 96.25% | 3.75% | 0.990 |
| LDA | 96.125% | 3.875% | 0.989 |
| Pruned Classification Tree | 94.125% | 5.875% | 0.965 |

Additional observations from the report:

- Random Forest performed very close to QDA, at 96.25% accuracy and 3.75% error.
- Boosting with shallow trees performed very close to LDA, at 96.125% accuracy and 3.875% error.
- Bagging lagged behind the leading models.
- The pruned tree was the weakest of the fully documented models, despite being the most interpretable tree-based option.

## Final Model Selection

Logistic Regression was selected as the final model because it delivered:

- the best cross-validated accuracy
- the lowest misclassification error
- an AUC close to 0.99
- a simpler and more interpretable structure than the more flexible alternatives

The ranking was also stable across resampling, which reinforces the robustness of the selection.

The final model was refit on the full training set and used to generate the output file:

- `output/advertising_test_with_predictions.csv`

## Repository Structure

The main production-ready project lives in `advertising dataset/`.

```text
advertising dataset/
|-- data/
|   |-- advertising_train.csv
|   |-- advertising_test.csv
|   `-- advertisingdatanote
|-- output/
|   `-- advertising_test_with_predictions.csv
|-- references/
|   |-- EDA_reference.Rmd
|   |-- EDA_reference.html
|   |-- Lab_activity4_resampling.Rmd
|   |-- Lab_activity5_classif_Part_1.Rmd
|   |-- Lab_activity6_classif_Part_2.Rmd
|   `-- Lab_activity7_trees.Rmd
|-- Advertising_Project_Final.Rmd
|-- Advertising_Project_Final.html
|-- advertising dataset.Rproj
`-- README.md
```

The HTML report is included for quick review, while the R Markdown source remains the reproducible entry point.

## Reproducibility

The project is built to run from within RStudio using relative paths.

### To reproduce the analysis

1. Open `advertising dataset.Rproj` in RStudio.
2. Install the required packages if needed.
3. Render `Advertising_Project_Final.Rmd`.
4. Review the generated HTML report.
5. Inspect the final predictions in `output/advertising_test_with_predictions.csv`.

### Core R packages

- `dplyr`
- `ggplot2`
- `visdat`
- `skimr`
- `corrplot`
- `lubridate`
- `here`
- `caret`
- `MASS`
- `tree`
- `ROCR`

## Key Findings

- A compact logistic model outperformed the more flexible alternatives on the evaluation protocol used in the project.
- Linear and quadratic discriminant methods were competitive, which suggests the class boundary is structured enough for classical statistical classifiers to work well.
- Tree ensembles were useful as benchmarks, but they did not materially beat the best linear model in this case.
- The pruned tree improved interpretability, but it did not match the top-performing classifiers in predictive strength.

## Technologies Used

| Category | Tools |
|---|---|
| Language | R |
| IDE | RStudio |
| Workflow | R Markdown |
| Data wrangling | `dplyr`, `lubridate` |
| Visualization | `ggplot2`, `visdat`, `corrplot` |
| Model evaluation | `caret`, cross-validation, confusion matrices, ROC/AUC |
| Statistical modeling | `MASS`, `tree` |
| Reporting | HTML output from R Markdown |

## Future Improvements

- Add regularized logistic regression to benchmark shrinkage against the current baseline.
- Expand model tuning with a more systematic hyperparameter search.
- Use nested cross-validation for an even stricter evaluation protocol.
- Add calibration analysis for predicted probabilities.
- Strengthen feature engineering from the timestamp field.
- Add automated rendering and validation steps for a cleaner publishing workflow.
