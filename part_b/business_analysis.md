## B1. Problem Formulation
(a) Machine Learning Formulation

The problem can be formulated as a supervised learning regression problem.

Target Variable:
items_sold (number of items sold per store per month)
Input Features:
Store attributes: store_size, location_type, footfall, competition_density
Promotion details: promotion_type (Flat Discount, BOGO, etc.)
Temporal features: month, is_weekend, is_festival
Historical performance: past sales, lag features
Problem Type:
Regression, because the goal is to predict a continuous numerical value (items sold).

Justification:
The business objective is to maximise sales volume, which is a continuous outcome. Therefore, regression is more appropriate than classification.

(b) Why Items Sold is Better than Revenue

Using items sold (sales volume) is more reliable than total revenue because:

Revenue is influenced by price changes and discounts, which vary across promotions
Promotions like discounts may increase volume but decrease revenue per item
Revenue can therefore misrepresent true demand

In contrast, items sold directly reflects customer demand and promotion effectiveness.

Broader Principle:
The target variable must align with the true business objective. Poor target selection can lead to misleading models and suboptimal decisions.

(c) Alternative Modelling Strategy

Instead of a single global model, a better approach is:

Segmented or hierarchical modelling

Options:

Build separate models by location type (urban, semi-urban, rural)
Or include interaction features (promotion × location)
Or use multi-level (hierarchical) models

Justification:
Different stores respond differently to promotions due to customer demographics and local factors. A segmented approach captures this variability and improves accuracy.

## B2. Data and EDA Strategy
(a) Data Joining and Dataset Design

The four tables would be joined as follows:

transactions → base table
Join with store attributes using store_id
Join with promotion details using promotion_id or month
Join with calendar using transaction_date

Final Dataset Grain:
One row = one store per month

Aggregations:

Total items_sold per store per month
Average basket size
Total footfall
Promotion applied that month

This ensures consistency between features and target.

(b) Exploratory Data Analysis (EDA)
Promotion vs Items Sold (Bar Chart)
Compare average sales across promotions
Helps identify strongest promotions
Time Series Plot (Monthly Sales)
Detect seasonality and trends
Guides creation of time-based features
Boxplot: Items Sold by Location Type
Understand differences across regions
May justify segmentation
Correlation Heatmap
Identify relationships between variables
Detect multicollinearity
Histogram of Items Sold
Check distribution and skewness
May suggest transformation

Impact on Modelling:
EDA informs feature engineering (e.g., seasonality features), model choice, and data transformations.

(c) Imbalance in Promotions

If 80% of data has no promotion:

Problems:

Model may learn to ignore promotions
Poor performance when predicting promotional impact

Solutions:

Oversample promotional cases
Use weighted models
Stratified sampling
Ensure evaluation includes promotion scenarios

## B3. Model Evaluation and Deployment
(a) Train-Test Split and Metrics

Split Strategy:

Use time-based split
Train on first ~2.5 years
Test on last ~6 months

Why not random split?

Causes data leakage from future into past
Unrealistic for forecasting problems

Evaluation Metrics:

RMSE: penalises large errors
MAE: easy to interpret average error
R² (optional): overall model fit

Interpretation:

Lower RMSE/MAE = better predictions
Compare errors relative to typical sales volume
(b) Explaining Model Recommendations

To explain why different promotions are suggested:

Use feature importance from models like Random Forest
Analyse key drivers:
Month (seasonality)
Festival periods
Store characteristics

Example:

December → festival season → Loyalty Points drives repeat purchases
March → normal period → Flat Discount drives immediate sales

Communication:
Translate technical insights into business language:

“During festive months, customer loyalty programs perform better, while discounts are more effective during regular periods.”

(c) Deployment Strategy

1. Model Saving

Save trained pipeline using joblib or pickle

2. Monthly Data Pipeline

Collect latest store + calendar + promotion data
Apply same preprocessing pipeline
Generate predictions for all stores

3. Automation

Schedule monthly batch job (e.g., cron, Airflow)

4. Monitoring

Track prediction error over time
Monitor:
RMSE/MAE drift
Data drift (feature distributions)
Sudden drops in performance

5. Retraining

Retrain model periodically (e.g., quarterly)
Or trigger retraining when performance degrades