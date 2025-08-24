# LaLiga Match Winner Prediction ⚽

## Phase 2 & 3: Data Cleaning & Preprocessing and Feature Engineering

This README.md documents the progress made in *Phase 2: Data Cleaning & Preprocessing* and *Phase 3: Feature Engineering* of the LaLiga Match Winner Prediction Machine Learning project.

---

### 🎯 Phase Goals Achieved:

*   *Data Cleaning & Preprocessing:* Ensured the raw data collected in Phase 1 is clean, consistent, and in the correct format for analysis and feature creation. This included handling data types (especially date), and initial checks for missing values.
*   *Feature Engineering:* Transformed raw match data into meaningful, predictive features that capture the dynamics of football matches. This involved creating time-aware metrics and encoding categorical information.
*   *Exploratory Data Analysis (EDA):* Performed initial EDA on the newly engineered features to understand their distributions, relationships, and readiness for model training.

---

### ✨ Key Deliverables & Techniques:

#### 1. Data Cleaning & Preprocessing

*   *Date Conversion:* Ensured the date column was correctly parsed into datetime objects, which is crucial for chronological sorting and time-series feature engineering.
*   *Initial NaN Handling:* Addressed any immediate missing values to prevent errors in subsequent steps.

#### 2. Feature Engineering

The core of this phase involved creating sophisticated features to represent team performance and identity:

*   *Rolling Averages for Team Form (_last_5 features):*
    *   Calculated 5-match rolling averages for goals_scored and goals_conceded for both home and away teams. This provides a dynamic measure of a team's recent offensive and defensive capabilities.
    *   Computed 5-match rolling rates for win, draw, and loss outcomes. These features capture the recent momentum and consistency of a team's results.
    *   *Crucially, these calculations were performed using closed='left' to prevent data leakage, ensuring that for any given match, only information from *previous matches is used.

*   *Team Identity (One-Hot Encoding):*
    *   Transformed categorical home_team and away_team names into numerical *one-hot encoded* features. This creates a binary column for each team (home_RealMadrid, away_Barcelona, etc.).
    *   This allows the machine learning model to learn the *inherent strength, unique characteristics, and home/away advantage* of each specific team, complementing the dynamic form metrics.

#### 3. Exploratory Data Analysis (EDA)

*   *Correlation Matrix:* Generated a correlation matrix (heatmap) to visualize the relationships between all engineered numerical features and the target variable (match_result_encoded). This step confirmed the expected correlations (e.g., between win_rate and loss_rate) and provided initial insights into which features might be most predictive.

---

### 📂 Code & Implementation:

All the code for data cleaning, preprocessing, and feature engineering for this phase can be found in:

*   ICoach_Data_Cleaning_Feature_engineer.ipynb

---

### ➡️ Next Steps:

With the data now clean and enriched with powerful features, the project will move into the core machine learning phases:

*   *Phase 4 – Data Splitting & Model Selection:*
    *   Splitting the dataset into training and testing sets *chronologically* to simulate real-world prediction scenarios.
    *   Selecting appropriate machine learning classification algorithms (e.g., Random Forest, Gradient Boosting) for predicting match outcomes.
*   *Phase 5 – Model Training & Evaluation:*
    *   Training the chosen model(s) on the prepared dataset.
    *   Rigorously evaluating model performance using relevant metrics (Accuracy, Precision, Recall, F1-score, Confusion Matrix).
*   *Phase 6 – Model Deployment & Monitoring:*
    *   Exploring options for deploying the trained model.
    *   Setting up monitoring for model performance over time.

---

Stay tuned for more updates as we continue to build a robust LaLiga match prediction model!