# LTFS Vehicle Loan Default Prediction & Early Warning Engine

[![Microsoft Fabric](https://img.shields.io/badge/Platform-Microsoft%20Fabric-blue?logo=microsoft)](https://fabric.microsoft.com/)
[![PySpark](https://img.shields.io/badge/Engine-PySpark%203.4-orange?logo=apachespark)](https://spark.apache.org/)
[![Power BI](https://img.shields.io/badge/Reporting-Direct%20Lake%20Mode-yellow?logo=powerbi)](https://powerbi.microsoft.com/)
[![Architecture](https://img.shields.io/badge/Architecture-Medallion%20(Delta%20Lake)-green)](https://delta.io/)

> An end-to-end Enterprise Financial Data Engineering, PySpark Machine Learning, and Power BI Direct Lake solution built on **Microsoft Fabric** to predict loan default risks across a **₹1,267+ Crore vehicle loan portfolio**.

---

## 📌 Executive Summary & Business Problem

In the Indian retail lending sector, Non-Performing Assets (NPAs) drastically impair a financial institution's capital adequacy and profitability. For non-banking financial companies (NBFCs) like **L&T Finance Holdings (LTFS)**, identifying high-risk accounts *before* they default allows branch managers and field agents to intervene early through targeted collection strategies, loan restructuring, or asset repossession.

This project ingests **233,154 real-world Indian vehicle loan accounts**, cleans credit bureau signals using **PySpark**, trains a **Random Forest Classifier** to assign an NPA probability score to every account, and serves sub-second analytics to executive leadership via **Power BI Direct Lake mode**.

---

## 📊 Business Metrics & Key Findings

| Metric | Value | Business Significance |
| :--- | :--- | :--- |
| **Total Portfolio Disbursal** | **₹ 1,267.36 Cr** | Total outstanding capital across 233,154 active vehicle loans |
| **Gross Historical Default Rate** | **21.70%** | 51,000 accounts historically defaulted, representing **₹ 270.28 Cr** at risk |
| **New-To-Credit (NTC) Segment** | **27,052 Accounts** | ~50% of defaulted accounts had zero prior bureau history (`CIBIL = 0`) |
| **ML Model Evaluation (AUC-ROC)**| **0.6307** | Evaluated on an 80/20 train-test split over un-augmented application-time attributes |
| **High Risk Tier Isolation** | **455 Accounts** | Isolated **₹0.55 Cr (₹55 Lakhs)** of extreme default risk for immediate field recovery action |

---

## 🏗️ Technical Architecture

```text
┌────────────────────────────────────────────────────────────────────────────────────────┐
│                                MICROSOFT FABRIC ONELAKE                                │
│                                                                                        │
│  ┌─────────────────┐      ┌──────────────────┐      ┌───────────────────────────────┐  │
│  │  Bronze Layer   │ ───► │   Silver Layer   │ ───► │          Gold Layer          │  │
│  │ (Raw Delta Lake)│      │(Cleansed Delta)  │      │(Analytical Feature Store)     │  │
│  └─────────────────┘      └──────────────────┘      └──────────────┬────────────────┘  │
│                                                                    │                   │
│                                                                    ▼                   │
│  ┌─────────────────┐      ┌──────────────────┐      ┌───────────────────────────────┐  │
│  │ Power BI Report │ ◄─── │ Direct Lake Mode │ ◄─── │ PySpark ML Scored Delta Table │  │
│  │ (2-Page Dash)   │      │(Semantic Model)  │      │(gold_loan_scores)             │  │
│  └─────────────────┘      └──────────────────┘      └───────────────────────────────┘  │
│                                                                                        │
└────────────────────────────────────────────────────────────────────────────────────────┘
                                           ▲
                                           │ Orchestrated via Sequential Execution
                                ┌──────────────────────┐
                                │ Fabric Data Pipeline │
                                └──────────────────────┘

Bronze Ingestion: Land raw 41-column CSV data into immutable Delta tables with system lineage timestamps.
Silver Transformation: Clean headers, parse loan age strings (2yrs 3mon → total months), standardize dates, handle nulls, and create CIBIL/LTV risk buckets.
Gold Feature Engineering: Build a multi-dimensional analytical feature store combining borrower demographics, credit bureau histories, and collateral metrics.
PySpark ML Scoring: Train a Random Forest model on 80% data split, evaluate on 20% test data, and score default probabilities (0–100%) for all accounts.
Direct Lake BI: Connect Power BI natively using Direct Lake on OneLake over gold_loan_scores to achieve sub-second query performance over 233,000+ rows without data duplication.
Pipeline Automation: Orchestrate the 4 sequential notebook execution steps via a Fabric Data Pipeline.
📁 Repository Structure
text

LTFS-Vehicle-Loan-Default-Fabric/
│
├── notebooks/
│   ├── 01_Bronze_Ingestion.py           # Ingestion from OneLake Files to Bronze Delta Table
│   ├── 02_Silver_Transformation.py      # Cleansing, PySpark UDFs, string parsing, risk bucketing
│   ├── 03_Gold_Feature_Engineering.py   # Gold Master feature store creation
│   └── 04_ML_NPA_Prediction.py          # PySpark ML Pipeline, Random Forest Classifier, probability scoring
│
├── screenshots/
│   ├── portfolio_risk_analytics.png     # Page 1 Power BI Dashboard (Portfolio Risk Analytics)
│   ├── early_warning_surveillance.png   # Page 2 Branch Early Warning Matrix (Early Warning Surveillance)
│   ├── pl_npa_daily_orchestration.png   # 4-Stage Fabric Pipeline Execution Proof
│   └── auc_roc_evaluation_score.png     # PySpark AUC-ROC Evaluation Score & Feature Importance output
│
└── README.md                            # Comprehensive Project Documentation
🛠️ Step-by-Step Implementation Breakdown
1. Data Processing & Feature Engineering (PySpark)
Loan Duration Normalization: Written a PySpark UDF to parse strings like '2yrs 3mon' or '5yrs 11mon' into total months (avg_acct_age_months & credit_history_months).
CIBIL Score Bucketing: Segmented numeric scores into 5 standardized categories: No History (NTC), Very Poor (300-549), Poor (550-649), Fair/Good (650-749), and Excellent (750+).
KYC Strength Index: Computed a combined identity verification score (0 to 5) summing flags across Aadhaar, PAN, Voter ID, Driving License, and Passport.
Collateral LTV Risk: Segmented Loan-To-Value percentages into High LTV (≥ 85%), Medium LTV (70-85%), and Safe LTV (< 70%).
2. Machine Learning Model (PySpark MLlib)
Pipeline Assembly: Implemented StringIndexer for categorical variables (employment_type, ltv_band, cibil_band) and packaged features using VectorAssembler.
Classifier: Trained a RandomForestClassifier (numTrees=100, maxDepth=10, seed=42).
Evaluation: Model evaluated using BinaryClassificationEvaluator achieving an AUC-ROC of 0.6307.
Risk Tiering: Extracted probability vector arrays to assign operational risk tiers:
🔴 High Risk: Default Probability ≥ 65%
🟡 Medium Risk: Default Probability 35% – 64.9%
🟢 Low Risk: Default Probability < 35%
3. Business Intelligence (Power BI Direct Lake)
Storage Engine: Connected natively using Direct Lake on OneLake over gold_loan_scores.
DAX Metrics: Written financial DAX measures dividing raw currency sums by 10,000,000 to format values in Indian Crores (₹ Cr).
Page 1 (Portfolio Risk Analytics): Highlights gross portfolio default rates, credit score default curves, collateral LTV distributions, and regional risk maps.
Page 2 (Early Warning Surveillance): Delivers a red/yellow/green color-coded field action table for branch managers to identify high-probability default accounts.
📷 Screenshots & Execution Receipts
1. Portfolio Risk Analytics (Page 1)
Portfolio Risk Analytics
<img width="1008" height="574" alt="Portfolio Risk Analytics" src="https://github.com/user-attachments/assets/5d5c6dde-a7b2-4a65-86b4-832c4493b5d1" />
2. Early Warning Surveillance (Page 2)
Early Warning Surveillance
<img width="1008" height="574" alt="Early Warning Surveillance" src="https://github.com/user-attachments/assets/7808d875-4829-42ee-a0b9-4583e3851840" />

3. Fabric Data Pipeline Orchestration
Fabric Data Pipeline Orchestration
<img width="1278" height="564" alt="pl_npa_daily_orchestration" src="https://github.com/user-attachments/assets/0962f373-9988-4d96-b456-cd62c1ede2d7" />

4. AUC-ROC Evaluation Score & Feature Importance
AUC-ROC Evaluation Score
<img width="964" height="213" alt="AUC-ROC evaluation score" src="https://github.com/user-attachments/assets/14f891b9-540f-4207-9267-82b6ed688e84" />

🎯 Operational Business Impact
Precision Field Collections: Instead of scattering recovery agents randomly across 233,000 borrowers, branch managers can filter Page 2 for 455 High-Risk accounts holding ₹55 Lakhs of capital, visiting them prior to the EMI due date.
Dynamic Underwriting Policy Adjustment: Dashboard insights reveal that over 27,000 borrowers are New-to-Credit (NTC), driving the highest default rates. The risk committee can instantly mandate a minimum 25% down payment (75% max LTV) for all future NTC applicants.
RBI Provisioning Compliance: The CFO gets an exact risk-weighted breakdown across Low, Medium, and High risk tiers to reserve capital required under Reserve Bank of India (RBI) guidelines.
⚙️ How to Replicate This Project
Prerequisites:

An active Microsoft Fabric Workspace (Trial or Capacity).
Dataset: LTFS Vehicle Loan Default Prediction Dataset (train.csv).
Setup Steps:

Create a Fabric Lakehouse named lh_credit_risk.
Upload train.csv into Files/raw/.
Import notebooks from the /notebooks folder into your workspace in order (01 to 04).
Run Notebook 01 through 04 sequentially.
Open the SQL Analytics Endpoint of lh_credit_risk → Create a New Semantic Model using Direct Lake on OneLake over gold_loan_scores.
Open the Power BI report editor, write the core DAX measures (Formatting in ₹ Cr and %), and build the report layout.
Create a Data Pipeline (pl_npa_daily_orchestration) linking all 4 notebooks sequentially (01_Bronze → 02_Silver → 03_Gold → 04_ML).
