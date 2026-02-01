# FRAUD-DETECTION-NOVAPAY
# Project Overview 
This project implements an end‑to‑end machine learning pipeline for transaction fraud detection using the nova_pay_combined.csv dataset. The solution covers data cleaning, feature engineering, exploratory analysis, model training, evaluation, explainability (SHAP), and model persistence.
The goal is to accurately identify fraudulent transactions while maintaining interpretability and business usability critical for financial services and payment platforms.
# Objectives
- Detect fraudulent transactions with high recall and strong ROC‑AUC
- Handle highly imbalanced fraud data effectively
- Engineer domain‑driven risk features (velocity, device trust, IP risk, account age)
- Compare multiple ML models and tune the best performer
- Provide model explainability for compliance and analyst review
- Deliver deployable artifacts (trained model + SHAP explainer)
# Dataset Description
The dataset contains historical transaction records with:
- Transaction amounts (source & USD)
- User and account attributes
- Device, IP, and location risk signals
- Velocity and behavioral indicators
- Target label: is_fraud (1 = Fraud, 0 = Legit)
Class distribution is highly imbalanced, reflecting real‑world fraud scenarios.
# Data Cleaning & Preprocessing
Key preprocessing steps include:
1. Data Quality Checks
- Null value analysis and handling
- Negative value detection and removal
- Future timestamp detection and filtering
2. Currency Normalization
- Derived exchange rates from known USD transactions
- Converted missing amount_usd using currency‑specific averages
3. Missing Value Strategy
- Fees filled by median (channel‑aware where applicable)
- IP country inferred from home country
- KYC tier filled using mode
- Device trust score filled using grouped medians
4. Categorical Normalization
- Standardized values for:
- Channel (web, mobile, atm)
- KYC tiers (low, standard, enhanced)
- Removed ambiguous values ("unknown")
# Feature Engineering
Time‑Based Features
- Hour of day
- Day of week
- Weekend indicator
- Night‑time activity flag (03:00–07:00)
Behavioral & Risk Features
- Account age buckets (new vs mature accounts)
- Transaction velocity flags (1h & 24h)
- High‑amount indicator
- High IP risk indicator
- Low device trust indicator
These features were informed by exploratory fraud‑rate analysis.
# Exploratory Analysis
Fraud rates were analyzed across:
- Channels (web, mobile, atm)
- KYC tiers
- Location mismatch
- Account age
- Transaction velocity
- Amount ranges
- IP risk score buckets
- Hourly activity patterns
Insights from EDA directly influenced feature thresholds.
# Train–Test Split Strategy
- Chronological split (80/20) to prevent data leakage
- Transactions sorted by timestamp
- Ensures realistic evaluation on future data
# Modeling Approach 
Preprocessing Pipeline
- One‑Hot Encoding for categorical features
- Standard Scaling for numeric features
- Implemented using ColumnTransformer
Models Trained
- Logistic Regression (baseline, class‑weighted)
- Random Forest (class‑weighted)
- XGBoost (scale_pos_weight)
- LightGBM (balanced)
Hyperparameter Tuning
- RandomizedSearchCV applied to Random Forest
- Optimized for F1‑score
# Model Evaluation
Models were evaluated using:
- Confusion Matrix
- Precision, Recall, F1‑Score
- ROC‑AUC
The Random Forest (tuned) achieved strong performance with a balance between recall and precision, making it suitable for production fraud screening.
# Model Explainability (SHAP)
To ensure transparency:
- SHAP TreeExplainer used for Random Forest
- Global feature importance derived from mean |SHAP|
- Local transaction‑level explanations implemented
- Explain Transaction Utility
- The explain_transaction() function:
- Displays true vs predicted label
- Shows prediction confidence
- Highlights top risk‑increasing and risk‑reducing features
This supports fraud analysts, compliance teams, and regulators.
# Model Persistence
The following artifacts are saved for deployment:
- rf_model.joblib → trained Random Forest model
- shap_explainer_rf.joblib → SHAP explainer
These can be loaded directly in production systems or APIs.
# Business Impact
1. Fraud Loss Reduction
- Early detection of high‑risk transactions reduces direct financial losses
- Velocity and behavioral features catch fraud before escalation
2. Improved Customer Experience
- Fewer false positives compared to rule‑only systems
- Legitimate customers face fewer unnecessary transaction blocks
3. Regulatory & Compliance Readiness
- SHAP explanations provide audit‑ready model transparency
- Supports explainability requirements (e.g., model governance, risk reviews)
4. Operational Efficiency
- Automated risk scoring reduces manual review workload
- Analysts can focus on genuinely high‑risk cases
5. Scalability
- Pipeline supports real‑time or batch scoring
- Modular design allows easy retraining and feature expansion
