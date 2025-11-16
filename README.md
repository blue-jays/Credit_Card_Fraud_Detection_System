# Reducing Credit Card Fraud Losses Through Transaction Pattern Analysis

## Executive Summary

**Goal**  
Identify transaction patterns that signal a higher risk of credit card fraud so that a card issuer can proactively flag and challenge suspicious activity before financial losses and customer churn occur.

**Method**  
Exploratory data analysis was performed on 99k+ historical card transactions, comparing fraudulent vs. legitimate activity by time of day, transaction amount, channel (online/POS/ATM), merchant group, geography, customer demographics, and bank. A simple machine learning model (Random Forest) was used to rank which variables are most predictive of fraud.

**Key Insight**  
Fraudsters cluster around **small online transactions (≤ £35) in the early hours of the day (midnight–7 a.m.)**, particularly with a small set of merchant categories and issuing banks. Over **50% of fraudulent transactions happen before or at 7 a.m.**, and about **80% of fraud is on amounts below £35**.

**Business Impact**  
By tightening controls on **low-value, early-morning online transactions** in the riskiest merchant and geography segments, the issuer can materially reduce fraud losses while keeping customer friction targeted and minimal. The patterns identified here can directly feed into rule-based alerts and as features for a production fraud model.

---

## Business Context

Every year, card issuers lose significant revenue to fraudulent transactions while also absorbing operational costs from chargebacks, investigations, and customer support. Excessive false positives, however, can frustrate genuine customers and drive them to competitors.

This analysis supports a **UK-focused credit card issuer** that wants to:

- **Reduce direct fraud losses** on card-not-present and low-value transactions.
- **Protect genuine customers** by intervening early in suspicious patterns without over-blocking.
- **Inform a next-generation fraud strategy**, including rules, limits, and a machine learning model.

The dataset contains **100,000 transactions** with a `Fraud` indicator (fraud vs. non-fraud) and attributes such as transaction time, amount, channel, merchant group, country of transaction, country of residence, age, gender, and bank.

---

## Approach

1. **Data preparation**  
   - Cleaned missing values and standardized amounts.  
   - Converted dates to proper datetime types and created analyst-friendly fields (e.g., hour of day).

2. **Exploratory analysis**  
   - Compared **fraud vs. non-fraud** distributions for:  
     - Time of day (`Time_(h)`)  
     - Transaction amount  
     - Transaction channel (`Online`, `POS`, `ATM`)  
     - Merchant groups  
     - Country of transaction vs. country of residence  
     - Issuing bank and customer demographics

3. **Feature importance (prototype model)**  
   - Trained a **Random Forest classifier** using encoded features.  
   - Ranked which variables contribute most to distinguishing fraud from normal transactions.

All code, data wrangling steps, and visualizations are documented in `test.ipynb`.

---

## Key Insights

### 1. Time of day is a strong fraud signal

- **52.07% of fraudulent transactions occur before or at 7 a.m.**  
- Only about **3.7% of all transactions** happen in this window, meaning fraud is **disproportionately concentrated at night/early morning**.  
- The model’s feature importance also ranks **transaction time** as one of the top drivers of fraud.

**Business takeaway:** The early hours (midnight–7 a.m.) are a **high-risk time band** that warrants tighter fraud controls.

### 2. Most fraud is on low-value transactions

- Around **79.1% of fraudulent transactions are for amounts ≤ £35**.  
- Fraudsters appear to favor smaller charges, likely to avoid drawing immediate attention.

**Business takeaway:** Low-value transactions are not inherently safe. Small amounts should still be risk-scored, especially in combination with other red flags (time, channel, merchant, geography).

### 3. Online transactions drive a large share of fraud

- Fraudulent transactions by channel:  
  - **Online:** ~44%  
  - **POS:** ~32%  
  - **ATM:** ~23%  
- For transactions ≤ £35, online still contributes the highest share of fraud.

**Business takeaway:** **Card-not-present (online) transactions** deserve stronger authentication, especially for small, off-hours purchases.

### 4. Specific merchant groups are over-represented

- Among fraudulent online transactions, certain merchant groups stand out:  
  - **Children-related merchants:** ~17.8% of fraud  
  - **Electronics:** ~13–14% of fraud  
  - **Fashion:** ~12–13% of fraud  

**Business takeaway:** These categories can be considered **high-risk segments** and prioritized for enhanced monitoring and dynamic limits.

### 5. Geography and bank exposure

- Fraudulent transactions are spread across several countries, with notable shares in **India, USA, China, Russia, and the UK**.  
- However, about **98% of cardholders are UK residents**, meaning UK customers are the primary victims even when the transaction occurs abroad.
- One bank (e.g., **Barclays**) shows the **highest share of fraudulent activity** in this dataset.

**Business takeaway:**  
- Cross-border and foreign-country transactions should receive **heightened scrutiny**, especially when inconsistent with the cardholder’s typical pattern.  
- Issuers with higher exposure (by fraud rate, not just volume) should prioritize targeted interventions and monitoring.

---

## Key Visuals for Executives

These visuals are generated from `test.ipynb`. Export each chart as a PNG into a `figures/` folder and update paths if needed.

1. **Fraud concentration by time of day**  
   Shows that over half of fraudulent transactions happen before or at 7 a.m., even though this is a small share of total volume.

   `figures/fraud_rate_by_hour.png`:

   ![Fraud rate by hour of day](figures/fraud_rate_by_hour.png)

2. **Fraud distribution by amount band**  
   Highlights that around 80% of fraudulent transactions are for amounts ≤ £35.

   `figures/fraud_amount_distribution.png`:

   ![Fraud distribution by amount band](figures/fraud_amount_distribution.png)

3. **Fraud share by transaction channel**  
   Compares Online vs POS vs ATM, showing that online transactions contribute the largest share of fraud.

   `figures/fraud_by_channel.png`:

   ![Fraud share by channel](figures/fraud_by_channel.png)

4. **Fraud exposure by merchant group**  
   Focuses on the top merchant groups associated with fraud (e.g., children’s products, electronics, fashion).

   `figures/fraud_by_merchant_group.png`:

   ![Fraud by merchant group](figures/fraud_by_merchant_group.png)

5. **Feature importance from prototype model**  
   Ranks which variables matter most for distinguishing fraud (e.g., time of day, geography, amount, merchant group).

   `figures/feature_importance.png`:

   ![Feature importance for fraud model](figures/feature_importance.png)

---

## Recommendations

Based on these patterns, the issuer can take the following actions:

1. **Introduce time- and amount-aware rules**  
   - Flag or step-up authenticate transactions that are:  
     - Occurring **between midnight and 7 a.m.**, and  
     - For **amounts ≤ £35**, especially when online.  
   - Use these rules as **pre-filters** for a fraud model and as standalone alerts.

2. **Tighten controls on high-risk online segments**  
   - Apply **stricter authentication (e.g., OTP, 3DS, device checks)** for:  
     - Online transactions in high-risk merchant groups (e.g., children’s items, electronics, fashion).  
     - Online purchases initiated from unusual locations relative to the customer’s typical behavior.

3. **Deploy bank- and geography-specific monitoring**  
   - Track **fraud rate by bank, country of transaction, and merchant group** on a regular dashboard.  
   - Prioritize **high-fraud-rate segments** for manual review, additional checks, or revised limits.

4. **Feed insights into the fraud model roadmap**  
   - Use **time of day, amount band, channel, merchant group, and geography** as key features in the next fraud detection model.  
   - Continuously backtest whether new rules and model changes lower fraud losses without excessively blocking genuine customers.

---

## Next Steps

This EDA is the foundation for a more robust fraud strategy. The next iterations will:

- **Address class imbalance** in the modeling stage (e.g., resampling or class-weighting) and re-evaluate feature importance.  
- Experiment with **alternate encoding strategies** (beyond simple label encoding) to ensure the model captures categorical patterns without creating unnecessary dimensionality.  
- Build and validate a **fraud risk scoring model** (starting with tree-based methods) and compare performance with/without the newly proposed rules.  
- Package the best-performing approach into a **deployable fraud rules and model pipeline** that can be integrated into the issuer’s transaction authorization flow.

All technical details and experiments are captured in the notebook; this document focuses on the **business story, key findings, and recommended actions** that could be shared directly with stakeholders.
