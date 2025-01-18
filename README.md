# Fantasy Premier League (FPL) Player Performance Prediction

**Author**: Abubakar Abdi  
**Final Project Report**

> **NOTE**: In this final report, I have updated the features and engineered additional features. While the project focuses primarily on players in the Premier League, remember that as an FPL manager, you only need the four best-performing players in each position.

---

## Table of Contents
1. [Introduction](#introduction)
2. [How Points Work](#how-points-work)
3. [Data Collection](#data-collection)
4. [Model Development](#model-development)
    - [Linear Regression](#linear-regression)
    - [Random Forest Regressor](#random-forest-regressor)
    - [XGBoost (Extreme Gradient Boosting)](#xgboost-extreme-gradient-boosting)
5. [Filtering Accuracy Based on Positions](#filtering-accuracy-based-on-positions)
6. [Residuals](#residuals)
7. [Key Findings](#key-findings)
8. [Next Steps](#next-steps)

---

## Introduction
The goal of this project is to develop a predictive model for Fantasy Premier League (FPL) player points in upcoming gameweeks, enabling FPL managers to make informed decisions about which players to include in their teams. Unlike models relying on full-season aggregates, this approach emphasizes:
- **Current-season gameweek data** to capture recent performance trends.
- **Position-weighted features** aligned with FPL scoring rules.

Data was collected from the 2020-21 to 2023-24 seasons for training, and the model was validated using data from the 2024-25 season. Although perfect accuracy is unachievable, this model provides valuable **relative comparisons** between players, aiding in optimal team selection each gameweek.

---

## How Points Work
Each player position (Goalkeeper, Defender, Midfielder, Forward) has a unique scoring system. Examples include:
- **Goalkeepers and Defenders** earn points for:
  - Clean sheets (+4 pts)
  - Saving a penalty (+5 pts)
- **Midfielders** get points for assists (+3 pts).
- **Forwards** get points for goals (+4 pts).
- **Special Cases**: A goalkeeper scoring a goal would receive a higher point bonus (e.g., +10 pts).

For more details, refer to the [Official FPL Website](https://fantasy.premierleague.com/).

---

## Data Collection
Data collection involved gathering and organizing all relevant FPL information:

1. **Player and Team Metadata**  
   - Fetched using FPL’s APIs (names, positions, performance metrics).

2. **Gameweek Data**  
   - Retrieved gameweek-specific stats per player (points, opponent, minutes played).

3. **Historical Data**  
   - Compiled data spanning past seasons (2020-21 to 2023-24) to enhance training.
   - Ensured consistent column naming and structure across all seasons.

4. **Feature Engineering**  
   - **Lagged Features**: e.g., previous gameweek total_points, minutes, goals_scored.  
   - **Rolling Averages**: 3-game rolling averages for total_points.  
   - **Position-Specific Metrics**: Weighted goals and clean sheets by position.  
   - **Aggregate Features**: Season-long totals, such as total_goals, total_clean_sheets, etc.

---

## Model Development
An essential consideration was ensuring all features were knowable **before** a gameweek started (e.g., last week’s points, but not future matches).

### Linear Regression
Used as a **baseline** model for interpretability and simplicity. 

- **Performance on Test Set**:
  - **MAE**: 1.13  
  - **RMSE**: 2.06  
  - **R² Score**: 0.23  

While straightforward, this model struggled with non-linear interactions and did not generalize well for outlier performances (e.g., players with double-digit points).

### Random Forest Regressor
An **ensemble** method combining multiple decision trees:

- **Performance on Test Set**:
  - **MAE**: 1.19  
  - **RMSE**: 2.13  
  - **R² Score**: 0.18  

Tended to underpredict rare high-scoring outliers, focusing on the broader population of average performers.

### XGBoost (Extreme Gradient Boosting)
A gradient-boosting approach optimized for minimizing errors, with hyperparameter tuning and the inclusion of engineered features (lagged values, rolling averages, position-based metrics).

- **Performance on Test Set**:
  - **MAE**: 1.06  
  - **RMSE**: 2.02  
  - **R² Score**: 0.26  

Despite a seemingly modest R² value, this **outperforms the other models** and aligns with or surpasses published benchmarks (e.g., [Research Paper], [FPL Review Website]).  
- The model performs particularly well for **Goalkeepers** (correlation ~0.61).

---

## Filtering Accuracy Based on Positions
The model’s performance improves when analyzed **per position**:
- **Goalkeepers**: Low variance and more consistent point totals = **lower RMSE**.
- **Forwards**: Reasonably predictable from goals and shot stats.
- **Midfielders** & **Defenders**: Slightly higher variance, especially defenders due to clean sheets being team-dependent.

Breaking down predictions by position allows for better targeted improvements and reveals where the model struggles or excels.

---

## Residuals
Residual analysis shows predictions are centered around zero but slightly **underestimate** higher-point outcomes. The model tends to predict moderate scores for all players, partly because:
- Most players earn between 1–5 points each gameweek.
- **Bonus points** are not fully captured, skewing predictions for top performers.

---

## Key Findings
1. **Overall Performance**  
   - **XGBoost** surpasses Linear Regression and Random Forest on MAE, RMSE, and R².  
   - Goalkeepers exhibit the highest correlation between actual and predicted points.
   
2. **Challenges**  
   - Model clustering around lower point values underestimates high-scorers.  
   - Variation in player performance (especially double-digit scorers) remains challenging.  

3. **Insights**  
   - **Position-specific metrics** and thorough **feature engineering** improve prediction.  
   - Past performance features (e.g., `bps_last_gameweek`, `avg_points_last_3`) are crucial predictors.  

---

## Next Steps
1. **Additional Predictors**:  
   - Incorporate player form, team performance, and match difficulty.
2. **Weighting Strategy**:  
   - Assign greater weight to higher-scoring players to handle outliers more effectively.
3. **Advanced Methods**:  
   - Further hyperparameter tuning.  
   - Additional ensemble methods or neural networks to capture complex patterns.  
   - Custom loss functions to emphasize accuracy for top-performing players.

---

_Thank you for reviewing this report! For any questions or further clarification, feel free to reach out or open an issue in this GitHub repository._
