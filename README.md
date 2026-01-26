# FINAL SUMMARY REPORT

## 📊 DATASET OVERVIEW

- Total Transactions: 10,757
- Fraud Cases: 982 (9.13%)
- Train Set: 8,605 rows (675 fraud)
- Test Set: 2,152 rows (307 fraud)

## 🎯 MODEL PERFORMANCE (Test Set)

| Model               | Precision | Recall | F1   | ROC-AUC |
| ------------------- | --------- | ------ | ---- | ------- |
| Logistic Regression | 96.64%    | 93.81% | 0.95 | 0.98    |
| Random Forest       | 100.00%   | 92.18% | 0.96 | 0.98    |
| XGBoost_SMOTE       | 99.30%    | 92.51% | 0.96 | 0.98    |
| LGBM_SMOTE          | 83.87%    | 93.16% | 0.88 | 0.97    |

## 🏆 BEST MODEL: Random Forest (However XGBOost Selected for Recall Score)

- Precision: 100.00%
- Recall: 92.18%
- Zero false positives: True

## 🔍 TOP FRAUD INDICATORS (SHAP / Feature Analysis)

1. dest_currency
2. home_country
3. source_currency_USD
4. device_trust_score
5. device_risk

## 💡 KEY BUSINESS INSIGHTS

- Fraud activity is strongly time-dependent, with elevated risk during early morning hours (3–7 AM), peaking around 5–6 AM. Fraud rates drop substantially during standard business hours, suggesting attackers exploit low-monitoring windows. Monthly fraud rates show limited variance, indicating no strong seasonal dependency. This supports the robustness of the model across time
- Transaction velocity signals show the strongest association with fraud: 24-hour velocity (ρ = 0.76) and 1-hour velocity (ρ = 0.70). Internal risk score also exhibits a strong relationship with fraud outcomes (ρ = 0.61), reinforcing the importance of behavioral and velocity-based features
- Account age is a dominant fraud risk factor: fraud rates peak during the first 90 days of account life and decline rapidly thereafter, suggesting early-lifecycle targeting by fraudsters
- Fraud risk peaks in the $2,000–$5,000 range, indicating attacker optimization for high-value transactions that avoid enhanced scrutiny.
- Fraud risk escalates rapidly once IP risk exceeds 0.7, suggesting IP reputation as a high-confidence signal suitable for hard controls or step-up verification

## 🚀 PROJECT OUTCOMES

- Achieved high precision with minimal customer friction
- Identified actionable fraud patterns for business rules
- Ready for production deployment

### Saved Artifacts

- **Trained Model**
  - The selected best-performing model (XGBoost) has been serialized and saved.
  - (fraud_model_xgb.joblib)

- **SHAP Explainer**
  - A SHAP `TreeExplainer` object has been saved to preserve the explainability
    configuration tied to the trained model.
  - This ensures consistency between model predictions and explanation logic.
  - (shap_explainer_xgb_joblib)

- **SHAP Values and Feature names**
  - Precomputed SHAP values for the test set have been saved.
  - These values are used for both global feature importance analysis and
    transaction-level explanations.
  - shap_values_xgb.npy
  - feature_names.json
