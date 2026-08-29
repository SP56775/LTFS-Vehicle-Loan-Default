# LTFS Vehicle Loan Default Prediction & Early Warning Engine
> **End-to-End Financial Data Engineering, PySpark Machine Learning & Direct Lake BI Solution built on Microsoft Fabric.**

## 📌 Business Context & Problem
L&T Finance Holdings (LTFS) and leading Indian NBFCs face critical credit risk when vehicle loans default. Identifying potential default accounts **before** they turn into Non-Performing Assets (NPAs) allows branch managers and risk officers to take early recovery action (restructuring, targeted field visits).

This project ingests **233,000+ real Indian vehicle loan accounts** (₹1,267+ Crore portfolio), runs PySpark feature transformations, trains a Random Forest risk classifier, and serves sub-second analytics to Power BI via **Direct Lake on OneLake**.

---

## 🏗️ Technical Architecture
`LTFS Raw CSV (233k Rows)` ➔ `Bronze Delta Table` ➔ `Silver Cleaned Table` ➔ `Gold Feature Store` ➔ `PySpark ML Model` ➔ `Gold Scored Predictions Table` ➔ `Power BI Direct Lake Dashboard` ➔ `Data Pipeline Automation`

---

## 🛠️ Stack & Capabilities
- **Platform:** Microsoft Fabric Capacity
- **Storage & Compute:** OneLake, Lakehouse, Delta Lake, SQL Analytics Endpoint
- **Data Engineering:** Apache Spark (PySpark), Medallion Architecture (Bronze/Silver/Gold)
- **Machine Learning:** PySpark MLlib (`RandomForestClassifier`, `VectorAssembler`, `BinaryClassificationEvaluator`)
- **Business Intelligence:** Power BI in **Direct Lake Mode on OneLake** (Zero Import/DirectQuery latency)
- **Orchestration:** Fabric Data Pipeline (5-stage sequential execution)

---

## 📈 Executive Key Performance Indicators (KPIs)
- **Total Disbursed Capital:** ₹ 1,267.36 Crores
- **Gross Default Rate:** 21.70% (51,000 Defaulted Accounts)
- **Defaulted Exposure:** ₹ 270.28 Crores
- **New-to-Credit (NTC) Default Concentration:** 27,052 borrowers had no prior credit history (CIBIL = 0), driving the highest proportion of portfolio default risk.
- **High Risk Tier Isolation:** PySpark ML isolated **455 extreme high-risk accounts holding ₹0.55 Crores**, giving field teams a targeted daily recovery matrix.

---

## 📷 Dashboard Preview
*(Insert links to your saved screenshots here)*
