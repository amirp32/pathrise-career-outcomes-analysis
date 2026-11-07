# Pathrise Career Outcomes Analysis

**Exploratory data analysis and machine learning for placement classification and program duration estimation.**

This portfolio project explores a Pathrise data challenge dataset through two Python notebooks. It examines participant characteristics, prepares mixed numerical and categorical data, and compares classification and regression models.

The project demonstrates data cleaning, visualization, feature engineering, model comparison, and critical evaluation of predictive results.

## Project questions

1. **Placement classification:** Can participant records be used to distinguish placed and non-placed fellows?
2. **Duration regression:** How well can the available features estimate `program_duration_days`?

The original business question concerns time to placement. The current regression notebook models recorded **program duration**, including non-placed participants, so its predictions should not be interpreted as time-to-placement estimates for new fellows.

## Notebooks

| Notebook | Purpose | Models |
| --- | --- | --- |
| [Placement classification](Pathrise_project_classification.ipynb) | Explore placement outcomes and compare classifiers | Logistic Regression, Support Vector Machine, Decision Tree, K-Nearest Neighbors |
| [Program duration regression](Pathrise_project_LinearRegression.ipynb) | Explore program duration and compare regression baselines | Linear Regression, Decision Tree Regressor, Support Vector Regression |

## Dataset

Both notebooks load an Excel workbook named **`Data_Pathrise.xlsx`**. Saved notebook outputs describe an initial dataset of **2,544 participant records**, with 15 columns after removing the `id` field.

| Field group | Examples |
| --- | --- |
| Program information | `pathrise_status`, `primary_track`, `cohort_tag` |
| Outcomes | `placed`, `program_duration_days` |
| Career background | `employment_status`, `professional_experience`, `highest_level_of_education` |
| Job search | `length_of_job_search`, `biggest_challenge_in_search`, `number_of_interviews`, `number_of_applications` |
| Other attributes | `work_authorization_status`, `gender`, `race` |

**Data availability:** The workbook is not included in the files supplied with this project. To rerun the analysis, obtain an authorized copy with the expected filename and columns. Saved charts and outputs can be inspected without the workbook.

## Analysis workflow

1. **Inspect the data:** Review column types, missing values, category counts, and outcome distributions.
2. **Explore relationships:** Visualize participant groups, cohort patterns, numerical distributions, and placement proportions using count plots, histograms, box plots, and heatmaps.
3. **Prepare features:** Remove identifiers, drop rows with missing program duration, apply IQR-based outlier filtering, fill selected missing values, derive cohort month, and one-hot encode categorical variables.
4. **Train models:** Use a 70/30 train/test split. The classification notebook also applies standard scaling and a four-fold grid search for Logistic Regression.
5. **Evaluate:** Compare classification metrics and confusion matrices; compare regression errors and inspect actual versus predicted duration.

## Recorded results

These figures are taken from the **saved notebook outputs**. They have not been independently reproduced for this README. They describe the existing experiments and require the methodological qualifications below.

### Placement classification

The comparison cell uses a 30% test split with `random_state=42`.

| Model | Accuracy | Precision | Recall | F1-score |
| --- | ---: | ---: | ---: | ---: |
| Logistic Regression | 95.30% | 0.946 | 0.961 | 0.954 |
| Support Vector Machine | 90.02% | 0.853 | 0.969 | 0.907 |
| Decision Tree | 99.80% | 1.000 | 0.996 | 0.998 |
| K-Nearest Neighbors | 71.23% | 0.663 | 0.872 | 0.753 |

Precision, recall, and F1-score use the notebook's binary metric defaults, with `placed=1` as the positive class. The classification report contains **511 test records**. The Decision Tree comparison does not set a random seed, so its result may vary between runs.

### Program duration regression

The comparison cell uses a 30% test split with `random_state=101`.

| Model | MAE (days) | RMSE (days) |
| --- | ---: | ---: |
| Linear Regression | 64.3 | 86.8 |
| Decision Tree Regressor | 80.3 | 119.8 |
| Support Vector Regression | 101.0 | 124.2 |

The separate Linear Regression evaluation records **R² = 0.520**. Linear Regression has the lowest saved MAE and RMSE among these three baselines; its MAE corresponds to approximately 64 days of average absolute error.

## Interpretation and limitations

- **Outcome-related features:** The classification notebook identifies and removes the `Placed` dummy variable because it perfectly matches the target. However, it retains other status indicators, such as withdrawal categories, and `program_duration_days`. These can reveal information unavailable at enrollment and may inflate apparent predictive performance. The 99.80% Decision Tree accuracy should not be presented as validated performance on future participants.
- **Preprocessing before splitting:** Imputation and outlier filtering occur before the train/test split; classification scaling also uses the full feature matrix. This weakens the separation between training and evaluation. A revised experiment should fit preprocessing only on training data and within each cross-validation fold.
- **Incomplete outcomes and selection:** Both notebooks drop records with missing duration. The saved outputs associate these missing values with Active, MIA, and Break statuses. Excluding these records changes the study population; an unfinished program is not equivalent to a confirmed non-placement outcome.
- **Duration is not necessarily time to placement:** The regression training data includes participants who were not placed and retains status-derived features. A dedicated time-to-placement analysis needs a clearly defined event, observation period, and treatment of censored records.
- **Demographic features:** Gender, race, and work authorization appear in the feature preparation. Inclusion in an exploratory notebook does not establish that they are appropriate for operational decisions. Group-level error analysis and a justified feature policy are needed before practical use.
- **Associations, not causal effects:** Differences between participant groups do not establish that a characteristic or program intervention caused an outcome.

## Run the notebooks

### Google Colab

The notebooks were written with a Colab upload workflow.

1. Download the notebook files or clone this repository.
2. Open a notebook in Google Colab.
3. Install the analysis dependencies in a setup cell if needed:

   ```python
   %pip install pandas numpy matplotlib seaborn scipy scikit-learn openpyxl tabulate
   ```

4. Run the `files.upload()` cell and select your authorized `Data_Pathrise.xlsx` workbook. Ensure the uploaded filename matches the name used by `pd.read_excel()`.
5. Run the remaining cells in order, addressing the compatibility notes below where needed.
6. Run the other notebook in a fresh runtime to keep its variables independent.

### Local Jupyter

Install the analysis dependencies and Jupyter:

```bash
python -m pip install jupyter pandas numpy matplotlib seaborn scipy scikit-learn openpyxl tabulate
jupyter notebook
```

Place `Data_Pathrise.xlsx` beside the notebooks. Skip the `from google.colab import files` and `files.upload()` cells, then run the remaining cells in order.

### Compatibility notes

The supplied files do not include a pinned environment, and the original notebooks use legacy patterns. The installation commands above provide the required packages, but are not a verified reproduction environment.

- Plotting cells use `sns.distplot`; these may need updating for the installed Seaborn version.
- Several cells use chained `fillna(..., inplace=True)` operations; explicit column assignment is preferable when updating the notebooks.
- Regression evaluation uses `mean_squared_error(..., squared=False)`; if the installed scikit-learn version rejects this argument, calculate RMSE as `np.sqrt(mean_squared_error(y_test, predictions))` using the corresponding model's predictions.

## Skills demonstrated

Python · Pandas · NumPy · Exploratory Data Analysis · Data Cleaning · Data Visualization · Feature Engineering · Scikit-learn · Classification · Regression · Cross-validation · Model Evaluation

## Next improvements

- Define which features are available at the intended prediction time and remove outcome-derived inputs.
- Build preprocessing and models into scikit-learn pipelines fitted only on training folds.
- Use stratified classification splits and evaluate generalization across cohorts.
- Compare against simple majority-class and mean/median duration baselines.
- Report uncertainty, probability calibration, and subgroup performance alongside aggregate scores.
- Reframe time-to-placement modeling around observed placements and censored participants.
- Add a tested, pinned environment and rerun both notebooks from a clean state.

## Project context

This repository presents an educational analysis of a Pathrise data challenge. It is not an official Pathrise product or a production hiring decision system. Dataset ownership and redistribution permissions remain with the relevant rights holders.
