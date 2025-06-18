# Financial Distress Risk Prediction: Altman Z-Score Regression Model for Turkish Companies

---

## Introduction

This project was developed as part of Akbank's Global AI Bootcamp. Our aim is to create a **machine learning model** that predicts the Altman Z-Score using financial data from publicly traded companies in Turkey. The Altman Z-Score is a crucial metric used to assess a company's financial health and potential bankruptcy risk. This study aims to add value to risk management and proactive decision-making processes in banking and finance.

---

## Dataset

The dataset used in this study is "Audit opinions of Turkish Public Companies" obtained from Kaggle. You can access the dataset via the following link:

[Kaggle Dataset Link](https://www.kaggle.com/datasets/agrafintech/financial-data-of-turkish-public-companies)

This dataset includes various financial ratios derived from company financial statements and bankruptcy prediction scores.

---

## Metrics

In this project, measuring the model's performance and interpreting its effectiveness on financial data is crucial. Given that the Altman Z-Score is a numerical value, we approached this as a **regression problem**. Therefore, we used the following metrics to evaluate model performance:

* **Mean Absolute Error (MAE):** Indicates the average absolute deviation of predictions from actual values. A lower MAE indicates more accurate predictions.
* **Mean Squared Error (MSE):** Represents the average of the squared errors. It penalizes larger errors more significantly.
* **Root Mean Squared Error (RMSE):** The square root of MSE, expressed in the original units of the target variable, making it easier to interpret.
* **R-squared Score (R2 Score):** Indicates how much variance in the target variable the model can explain. It ranges from 0 to 1; the closer to 1, the better the model.

### Problem Definition and Approach Change

Initially, there was an idea to treat the Altman Z-Score as a **classification problem** by categorizing it into bankruptcy risk categories. However, due to the continuous numerical nature of the Z-Score itself, it was realized that this approach overly simplifies the data and limits the model's learning capacity. Given that the Z-Score is already an indicator of risk, **directly predicting its numerical value** provides a more meaningful and functional approach. Therefore, the project scope shifted to developing a **regression model** to predict the numerical value of the Altman Z-Score using other financial indicators. This change allowed the model to capture deeper relationships between financial data and risk.

### Data Preprocessing and Feature Engineering

The dataset underwent extensive preprocessing and feature engineering steps:

* **Categorical Variable Handling:** The 'Period' column was labeled with relevant month counts, and the 'Company Code' column was transformed using Label Encoding. The 'Company Name' column was removed as the 'Company Code' provided sufficient information.
* **Missing Value Analysis:** The significance of missing values in categorical variables such as 'Opinion Type' was evaluated concerning model relevance and general data distribution.
* **Outlier Analysis and Decision:** Common outliers in financial data were identified using Boxplot visualizations. The decision was made to **retain** these outliers in the dataset based on their potential significance in financial risk assessment. This approach was supported by the use of outlier-resistant regression models.
* **Data Distribution and Skewness:** Significant right skewness and high variances in financial ratios were accepted as natural features of the data. Therefore, no variable transformation was performed to preserve model interpretability and retain important financial signals.
* **Correlation Analysis and Feature Selection:** Correlations between the Altman Z-Score and other variables were examined. Liquidity ratios (Current Ratio, Acid Test Ratio, Cash Ratio) showed positive correlations, while some bankruptcy scores exhibited negative correlations. Variables with correlation coefficients absolute value less than $0.01$ were **removed** during feature engineering as they did not contribute significantly to the model. This streamlined the model, making it more straightforward and effective.

### Model Selection and Training: Random Forest Regressor

A **Random Forest Regressor** model was chosen to predict the Altman Z-Score due to its ability to handle the continuous, wide range, high variance, and outlier-rich nature of the target variable. Reasons for choosing this model include:

* **Robustness to Outliers:** Being a tree-based model, it naturally provides robustness against outliers.
* **Capturing Non-linear Relationships:** Successfully learns complex and non-linear relationships in financial data.
* **Resistance to Overfitting:** Reduces overfitting and enhances generalizability by combining multiple decision trees.

For model training, significant variables identified during correlation analysis (`X`) were used as features, while the target variable (`y`) was the **numerical value of the Altman Z-Score**.

### Model Evaluation and Optimization

Initially, the developed Random Forest regression model showed high performance. It achieved an impressive R-squared score of $0.9903$ on a single test set and confirmed this performance's generalizability ($0.9693$ Average R-squared) through cross-validation. These results indicated that the model reliably predicted the Altman Z-Score.

To further optimize model performance and find the best hyperparameter combination, we used **GridSearchCV**. This process systematically searched for the best performance (lowest RMSE) within defined parameter ranges.

**GridSearchCV Results:**

GridSearchCV determined the most suitable hyperparameters for the model as follows:

* **Best Hyperparameters:** `{'max_depth': 20, 'max_features': 1.0, 'min_samples_leaf': 1, 'min_samples_split': 2, 'n_estimators': 100}`
* **Best Mean RMSE (GridSearchCV Result):** $7.2445$

These results demonstrate that during cross-validation, the model achieved its best error rate with these parameters.

**Optimized Model's Final Test Set Performance:**

After retraining the model with the hyperparameters identified by GridSearchCV and evaluating it on a completely unseen test set, we obtained the following results:

* **Mean Absolute Error (MAE):** $0.0996$
* **Mean Squared Error (MSE):** $14.0504$
* **Root Mean Squared Error (RMSE):** $3.7484$
* **R-squared Score (R2 Score):** $0.9856$

**Evaluation:**

The performance metrics of the optimized model on the test set indicate that it maintained the high performance observed initially and even improved on some metrics:

* **R-squared Score** of $0.9856$ remains **extremely high**, indicating that the model can explain nearly 99% of the variance in the Altman Z-Score and retains its predictive power.
* **MAE ($0.0996$) and RMSE ($3.7484$)** values are significantly lower than the previous cross-validation averages (Average MAE: $0.3937$, Average RMSE: $7.0972$). This improvement demonstrates that **hyperparameter optimization successfully enhanced the model's generalization performance** and minimized prediction errors.

In conclusion, your Random Forest Regressor model has evolved into a robust and optimized structure capable of **accurately and reliably predicting the Altman Z-Score**. This model can be effectively used in critical business scenarios such as financial risk assessment and forecasting potential bankruptcy risks for companies.

---

## Conclusion and Future Work

The Random Forest regression model developed in this project demonstrated superior performance in predicting the Altman Z-Score using financial data from Turkish companies. The high R-squared score and low error metrics indicate that the model could be a powerful tool for financial risk assessment and proactive management processes. This study provides valuable insights into predicting company financial health and managing potential bankruptcy risks in the banking sector.

### Vision for Future Work

This project serves as a starting point and can be further developed in several directions:

* **Dynamic Data Collection:** Integrate the current static dataset with dynamic, real-time financial data streams to keep the model continuously updated. This could involve automatically fetching data from stock exchanges or financial data providers via APIs.
* **Model Expansion:** Develop models that predict not only the Altman Z-Score but also other bankruptcy scores like Springate, Zmijewski, or combine these scores to create a comprehensive risk score.
* **Deep Learning Approaches:** Experiment with deep learning models such as LSTM or Transformer, especially for time series data, to better capture financial trends and dynamics.
* **Interface Integration:** Develop a simple web interface (using Flask or Streamlit, for example) to make model predictions easily accessible. This would enable financial analysts or bankers to perform instant risk assessments.
* **Explainable AI (XAI):** Integrate XAI tools like SHAP or LIME to explain why the model made specific Altman Z-Score predictions. This would enhance the model's reliability and adoption by business units.
* **Integration of Macroeconomic Factors:** Enhance prediction accuracy and robustness by including macroeconomic indicators such as inflation, interest rates, GDP, alongside company-specific data.

This project is a significant step for me in advancing my proficiency in machine learning and gaining applied insights into real-world problems. In the future, I plan to continue working on such projects to achieve my career goal at the intersection of financial technologies (FinTech) and artificial intelligence.

---

## Links

* [Kaggle Notebook Link](https://www.kaggle.com/code/ariffurkanaytekin/akbank-bootcamp)
