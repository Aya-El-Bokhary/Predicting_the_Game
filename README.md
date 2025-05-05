# Predicting_the_Game
A Data-Driven Approach to Football Outcomes 

This project aimed to forecast football match outcomes in the English Premier League over four seasons (2021–2025). We manually compiled, cleaned, and engineered data from FBref.com, applying machine learning models to predict match results across three classes: Home Win, Draw, Away Win.
Data Preparation

1. Merge per-season sheets
   - Concatenated all team-stat Excel sheets into one “mixed” file per season.
   - Aligned each match to its home (-H) and away (-A) team stats.

2. Chronological Concatenation
   - Stacked all seasons in date order to form the raw visualization workbook.
   - Saved as: all_seasons_2021-2025_raw_visualization_data.xlsx

3. Combine & Clean
   - Merged match results with team stats into all_seasons_2021-2025_combined_team_stats.xlsx.
   - Ensured feature consistency and dropped duplicates.

4. Final Model-Ready Dataset
   - Engineered lagged, rolling, head-to-head, and delta features.
   - Filtered out first-ever matches and imputed missing values.
   - Final file: all_seasons_2021-2025_cleaned_model_data.xlsx

Feature Engineering
- Rolling Averages (10 matches): For each team, we computed rolling averages over the past 10 matches for goals, xG, points, and other performance stats. These were stored as features with `_last10` suffix.
- Exponentially Weighted Moving Averages (EWM): Calculated with spans of 5 (`_ewm5`) and 10 (`_ewm10`) to better reflect short-term and medium-term momentum, giving more weight to recent games.
- Head-to-Head Features: Goal differential history between the two teams.
- Delta Features: Difference between home and away stat values (e.g., possession, shots, passes).
- Temporal Features: Days of rest before match and cyclical month encoding.
- Contextual Info: Log-transformed attendance to stabilize variance.
Modeling Pipeline

1. Imputation & Conversion: Converted percentages and filled missing values
2. Class Imbalance Handling: Used class_weight='balanced' and SMOTE
3. Time-Series Cross-Validation: Preserved temporal order with TimeSeriesSplit
4. Hyperparameter Tuning: GridSearchCV optimized for macro-F1
5. Calibration & Thresholding: Calibrated LightGBM and used a tuned threshold for draw class
6. Stacking Ensemble: Combined HistGB, LightGBM, and RF using logistic meta-learner

Model Performance
Model	Accuracy	Macro-F1	Draw Recall	Notes
HistGradientBoosting	61.4%	0.542	16.9%	Best single model overall
RandomForest (balanced)	~56%	~0.45	~14%	Baseline
GradientBoosting	~58%	~0.50	~15%	Good, but below HistGB
LightGBM + Thresholding	~57.8%	~0.508	~14.5%	Tuned threshold improved draw recall
Stacking (HistGB + LGBM + RF)	~58.7%	~0.555	~31%	Best draw recall, slight accuracy dip
Dataset Files Overview

- all_seasons_2021-2025_raw_visualization_data.xlsx: For exploratory charts and plots
- all_seasons_2021-2025_combined_team_stats.xlsx: Intermediate merged file before feature engineering
- all_seasons_2021-2025_cleaned_model_data.xlsx: Final model-ready version
- season_2022-2023_feature_set.xlsx to season_2024-2025_feature_set.xlsx: Per-season snapshots

Code Structure
- The project includes three main code files:
  • `visualization_code`: Used for exploratory data analysis and chart generation, based on the raw dataset.
• `statistical_code`: Used for experimental testing and benchmarking different preprocessing techniques.
  • `ml_code`: Used for modeling and prediction, including data preprocessing, feature engineering, machine learning model training, and evaluation, which was the most reliable approach for our problem at hand.

