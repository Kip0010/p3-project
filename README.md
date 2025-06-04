## 1. Business Understanding
   
Objective: Predict who is likely/unlikely to get the H1N1 vaccine based on survey data to help public health organizations target outreach efforts more effectively.

Stakeholders:

-Public Health Officials: Optimize vaccine distribution.

-Behavioral Scientists: Understand factors influencing vaccine decisions.

-Policy Makers: Design better awareness campaigns.

-Problem Type: Binary classification (1 = vaccinated, 0 = not vaccinated).

Why ML?

Traditional methods (e.g., averages, graphs) can't capture complex interactions between demographics, beliefs, and behaviors. ML can:

-Predict outcomes for new individuals.

-Rank influential factors (e.g., doctor recommendations).

-Identify unexpected patterns.

2. Data Understanding
   
Dataset: 2009 National H1N1 Flu Survey (26,707 respondents, 36 features + 2 targets).

Key Features:

-Demographics: Age, education, income, employment.

-Behaviors: Handwashing, mask usage, etc.

-Opinions: Perceived vaccine risk/effectiveness.

-Medical: Doctor recommendations, chronic conditions.

Target Variables:

-h1n1_vaccine: Primary focus (21.25% vaccinated, 78.75% not → imbalanced classes).

-seasonal_vaccine: Secondary (46.56% vaccinated).

Data Issues:

-Missing values in 30/36 columns (e.g., employment_industry had 13,330 missing).

-Mixed data types: binary, ordinal, categorical, numeric.

3. Data Preparation
   
Handling Missing Values:

-High-missing columns (e.g., employment_industry): Filled with "Missing" as a new category.

-Opinion columns: Filled with median values (neutral imputation).

-Binary columns: Filled with mode (most frequent value).

-Household counts: Filled with median.

Feature Encoding:

-Ordinal Encoding: For ordered categories (e.g., education: "<12 Years" → 0, "College Graduate" → 3).

-One-Hot Encoding: For nominal categories (e.g., employment_status, race).

Feature Scaling:

-Standardized numeric features (e.g., opinion_h1n1_risk) using StandardScaler (mean=0, std=1).

Train-Test Split:

-Stratified split (80% train, 20% test) to preserve class distribution.

4. Modeling
Baseline Models
a) Linear Regression:

Poor performance (ROC-AUC: 0.30, recall: 21% for class 1).

Unsuitable for classification (outputs probabilities, not classes).

b) Logistic Regression:

Better recall (58%) but moderate precision (49%).

Key predictors: Doctor recommendations (+0.81 coefficient), positive vaccine opinions.

ROC-AUC: 0.85.

c) Random Forest:

Best balance: precision (55%), recall (63%), F1-score (60%).

ROC-AUC: 0.85.

Top features: Doctor recommendations, health insurance, vaccine opinions.

Hyperparameter Tuning
Logistic Regression: Best parameters: C=0.1, penalty='l2', class_weight='balanced'.

Random Forest: Best parameters: max_depth=20, min_samples_leaf=7, class_weight='balanced_subsample'.

5. Model Evaluation
Metrics at Threshold = 0.323:

Accuracy: 82.2%

Precision: 57.3%

Recall: 63.1%

F1-score: 60.1%

Confusion Matrix:

Predicted 0	Predicted 1
Actual 0	3,674 (TN)	533 (FP)
Actual 1	419 (FN)	716 (TP)
Key Insights:

Model is better at identifying non-vaccinated individuals (high TN).

Lower precision means false positives (predicting vaccination incorrectly).

Optimized threshold improves recall (catches more vaccinated cases).

SHAP Analysis:

Top predictors:

Doctor recommendations (+0.16 mean |SHAP|).

Perceived vaccine effectiveness (+0.12).

Health insurance (+0.11).

Negative predictors: Certain occupations (e.g., employment_occupation_tfqavkke).

6. Deployment Pipeline

Steps:

-Preprocessing: Impute missing values, encode categories, scale features.

-Modeling: Load trained Random Forest.

-Prediction: Output vaccination probability.

Example Prediction:

-Input: doctor_recc_h1n1=1, opinion_h1n1_risk=4 → 66% vaccination probability.
