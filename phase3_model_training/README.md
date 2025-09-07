# Model Training and Tuning Phase

This document outlines the methodologies and progress made during the model training and hyperparameter tuning phase of the football match prediction project. The primary goal of this phase is to develop robust predictive models capable of accurately forecasting match outcomes (Home Win, Draw, Away Win) based on historical data.

## 1. Data Preparation and Loading

The initial data preparation, including cleaning, feature creation (like one-hot encoded teams), and the crucial chronological train/test split, was completed in the previous phase. For this stage, the pre-split datasets (X_train.parquet, X_test.parquet, y_train.parquet, y_test.parquet) are loaded.

## 2. Initial Model Baselines & Data Leakage Correction

### Initial Approach
*   *Models:* Logistic Regression and Random Forest Classifiers were initially used to establish baseline performance.
*   *Features:* The initial feature set included basic match statistics and one-hot encoded team identifiers.

### Critical Data Leakage Identification and Resolution
Upon initial evaluation, the models exhibited unrealistically high accuracy (approaching 100%). This indicated a severe data leakage issue. The root cause was identified as the inclusion of home_team_goal and away_team_goal as features in the input data (X). These features directly represent the match outcome, making prediction trivial and invalid.

*Resolution:* home_team_goal and away_team_goal were removed from the feature set (X).

### Post-Leakage Correction Baseline
After removing the leaked features, model performance dropped to a more realistic range (around 51-52% accuracy). This confirmed the models were now learning from truly predictive, pre-match information. However, performance on predicting 'Draws' (Class 1) was notably poor, characterized by very low recall and F1-scores.

## 3. Advanced Preprocessing and Feature Engineering

To improve model performance, especially for the underrepresented 'Draw' class, and to provide more predictive signals, the following steps were taken:

### a. Feature Scaling
*   *Method:* StandardScaler was applied to numerical features. This is crucial for distance-based algorithms (like Logistic Regression) and gradient-based methods, ensuring features contribute equally to the model's learning process.
*   *Strategy:* Scaling is performed after the train/test split to prevent data leakage from the test set. The scaler is fit only on the training data and then used to transform both training and test sets.

### b. Addressing Class Imbalance (for Draws)
*   *Problem:* The 'Draw' class is a minority class, leading to models biased towards predicting 'Win' or 'Loss' outcomes.
*   *Solution:*
    *   *Class Weights:* class_weight='balanced' was used in applicable models (e.g., Logistic Regression, Random Forest) to penalize misclassifications of minority classes more heavily.
    *   *Synthetic Minority Over-sampling Technique (SMOTE):* The imbalanced-learn library's SMOTE was integrated into the modeling pipeline. SMOTE generates synthetic samples of the minority class (Draws) to balance the dataset during training. This is applied within the cross-validation folds to prevent leakage.

### c. Advanced Feature Engineering (Team Form)
Recognizing that current team form is a critical predictor, sophisticated features were engineered:
*   *Rolling Averages:* For each team in every match, rolling averages over the last 5 games were calculated for:
    *   Goals Scored (avg_goals_scored_last_5)
    *   Goals Conceded (avg_goals_conceded_last_5)
    *   Win Rate (win_rate_last_5)
    *   Draw Rate (draw_rate_last_5)
    *   Loss Rate (loss_rate_last_5)
*   *Chronological Calculation:* These features were meticulously calculated using groupby().transform().rolling(closed='left') on a long-format DataFrame, ensuring that for any given match, the form statistics only reflect data from prior matches, thereby preventing data leakage.
*   *Integration:* These calculated features were then merged back into the main DataFrame, creating home_avg_goals_scored_last_5, away_win_rate_last_5, etc.

## 4. Model Selection and Hyperparameter Tuning

### Models Explored
*   *Logistic Regression:* A strong baseline classifier.
*   *Random Forest Classifier:* An ensemble tree-based model known for good performance and robustness to feature scaling (though still used with scaling in pipeline).
*   *XGBoost Classifier:* A powerful gradient boosting algorithm, often achieving state-of-the-art results on tabular data.

### Hyperparameter Tuning Strategy
*   *Methodology:* GridSearchCV was employed for systematic hyperparameter optimization.
*   *Cross-Validation:* Crucially, TimeSeriesSplit was used for cross-validation. This maintains the chronological order of the data, ensuring that models are always validated on future data, which is essential for time-series prediction tasks like football.
*   *Pipeline Integration:* All preprocessing steps (scaling, SMOTE) and the classifier were chained together using imblearn.pipeline.Pipeline. This ensures that preprocessing is correctly applied within each cross-validation fold during tuning, preventing data leakage.
*   *Evaluation Metric:* f1_weighted was primarily used as the scoring metric for GridSearchCV to ensure a balanced evaluation across all classes, especially given the class imbalance.

## 5. Current Status

The latest round of hyperparameter tuning for Random Forest and XGBoost, incorporating the newly engineered team form features and the SMOTE oversampling technique within a TimeSeriesSplit cross-validation pipeline, has been executed. The results are currently being analyzed to assess the impact of these improvements on overall accuracy and, critically, on the prediction of 'Draws'.

## 6. Future Work

Based on the analysis of the current results, the next steps may include:
*   *Further Feature Engineering:*
    *   Implementing *Head-to-Head (H2H)* statistics (e.g., historical results between the specific two teams).
    *   Incorporating *Underlying Performance Metrics* like Expected Goals (xG) and Expected Goals Against (xGA) if available.
    *   Developing *Streak* features (e.g., number of consecutive wins/losses).
*   *Ensemble Modeling:* Combining the predictions of the best-performing individual models (e.g., using VotingClassifier or StackingClassifier) to achieve more robust and potentially higher accuracy.
*   *Error Analysis:* Deep diving into misclassified predictions to identify patterns and inform further feature engineering or model refinements.
*   *Model Interpretation:* Analyzing feature importance to understand which factors contribute most to predictions.