# 📊 Analyzing Payment Performance and Transaction Behaviors for an E-Wallet Company using Python  
![Image](https://github.com/user-attachments/assets/0d4a7fd2-2793-4939-b0ec-40ddf311121f)

Author: Hoang Thi Hong Nhung 
Date: 2025-09-03  
Tools Used: Python

## 📑 Table of Contents  
1. [📌 Background & Overview](#-background--overview)  
2. [📂 Dataset Description & Data Structure](#-dataset-description--data-structure)  
3. [🔎 Main Process](#-main--process)
4. [🎯 Conclusion](#-conclusion)

---

## 📌 Background & Overview  
#### 🎯 Objective
Analyze e-wallet payment & transaction datasets to answer:

✔️ What is the current situation of payment volume and transactions?

✔️ Which products/teams perform best and worst?

✔️ How are refunds distributed by sources?

✔️ How do different transaction types contribute to customer behavior?

#### 👤 Who is this project for?

✔️ Data Analysts & BI specialists in fintech/e-wallet companies

✔️ Payment operations & product teams

✔️ Decision-makers designing customer experience & fraud detection initiatives

## 📂 Dataset Description & Data Structure  
#### 📌 Table Used  
payment_report.csv → Monthly payment volume of products
product.csv → Product metadata (team, category, ownership)
transactions.csv → Transaction-level information (transType, merchant, amount, sender, receiver)

#### Data definition & example
##### Table 1: Payment_report
<details>
  <summary>Payment_report</summary>
| Column Name | Data Type | Description                          |
| ----------- | --------- | ------------------------------------ |
| product\_id | INT       | Unique identifier for each product   |
| category    | TEXT      | Product category (e.g., PXXXXXB)     |
| team\_own   | TEXT      | The team responsible for the product |
<details>
<details>
  <summary>Example:</summary>
  
| product\_id | category | team\_own |
| ----------- | -------- | --------- |
| 17          | PXXXXXB  | ASD       |
| 18          | PXXXXXB  | ASD       |
| 20          | PXXXXXB  | ASD       |
<details>
  
##### Table 2: Payment Report Table
<details>
  <summary>Payment Report Table</summary>
| Column Name    | Data Type | Description                               |
| -------------- | --------- | ----------------------------------------- |
| report\_month  | DATE      | Month of report (YYYY-MM)                 |
| payment\_group | TEXT      | Type of payment (e.g., payment, refund)   |
| product\_id    | INT       | Reference to product\_id in Product Table |
| source\_id     | INT       | Source identifier of payment              |
| volume         | FLOAT     | Total payment volume                      |
<details>

<details>
  <summary>Example</summary>
  
| report\_month | payment\_group | product\_id | source\_id | volume    |
| ------------- | -------------- | ----------- | ---------- | --------- |
| 2023-01       | payment        | 12          | 45         | 624110375 |
| 2023-01       | payment        | 17          | 45         | 335715113 |
<details>

##### Table 3: Transactions Table
<details>
  <summary>Transactions Table</summary>
| Column Name     | Data Type | Description                         |
| --------------- | --------- | ----------------------------------- |
| transaction\_id | BIGINT    | Unique ID for each transaction      |
| merchant\_id    | INT       | Identifier of merchant              |
| volume          | FLOAT     | Transaction amount                  |
| transType       | INT       | Transaction type code               |
| transStatus     | INT       | Status of transaction (1 = success) |
| sender\_id      | FLOAT     | Sender’s customer ID                |
| receiver\_id    | FLOAT     | Receiver’s customer ID              |
| extra\_info     | TEXT      | Additional metadata                 |
| timeStamp       | BIGINT    | Unix timestamp of transaction       |
<details>

<details>
  <summary>Example:</summary>
| transaction\_id | merchant\_id | volume | transType | transStatus | sender\_id | receiver\_id | timeStamp     |
| --------------- | ------------ | ------ | --------- | ----------- | ---------- | ------------ | ------------- |
| 3002692434      | 5            | 100000 | 24        | 1           | 10199794   | 199794       | 1682932054455 |
| 3002692437      | 305          | 20000  | 2         | 1           | 14022211   | 14022211     | 1682932054912 |
<details>
  
## ⚒️ Main Process
#### 3.1. Exploratory Data Analysis (EDA)  

#### 3.2. Data Cleaning & Preprocessing
**Payment_enriched**

*Missing data:*
- category: 22 null values => Change to others
- team_own: 22 null values => Change to others

*incorrect data types*
- report_month (type: object) => Action: Change to datetime
- source_id (type: int64) => Action: Change to strings
- product_id (type: int64) => Action: Change to strings

**Transactions**

*Duplicate data*
- transaction_id => Action: Drop duplicate data

*Missing data:*
- sender_id: 49059 null values => No Action as senders could be already deleted in system
- receiver_id: 164795 null values => Action: Delete rows with receiver_id null as receiver information could be
- extra_info: 1317907 null values => No Action

*Incorrect data types*
- transaction_id (type: int64) => Action: Change to strings
- merchant_id (type: int64) => Action: Change to strings
- transType (type: int64) => Action: Change to strings
- transStatus (type: int64) => Action: Change to strings
- sender_id (type: float64) => Action: Change to strings
- receiver_id (type: float64) => Action: Change to strings
- timestamp (type: object) => Action: Change to datetime

#### 3️.3. Data wrangling
| # | Business Question | Purpose | Answer | Analysis / Insight |
|---|---------|---------|--------|------------------|
| 3.3.1 | Which **3 product_ids** have the highest payment volume? | Identify top-performing products by revenue | 1976: 61,797,583,647<br>429: 14,667,676,567<br>372: 13,713,658,515 | These products generate the majority of payment volume. Focus on their operational efficiency and monitoring. |
| 3.3.2 | Are there any products that are **owned by multiple teams**? | Ensure **1 product → 1 team** rule is maintained | No | All products comply with ownership rules, ensuring accountability and simplifying performance tracking. |
| 3.3.3 | Which **team has the lowest performance** since Q2 2023? Which category contributes least to that team? | Identify underperforming teams and weak product categories for targeted improvement | Team: APS<br>Lowest contributing category: 25,232,438 | APS team may need operational support or process improvement. Focus on the low-contributing category to boost overall performance. |
| 3.3.4 | Among **refund transactions**, which **source_id** contributes the most? | Understand refund sources for risk/fraud monitoring | Source_id: 38 | This source accounts for the highest refund volume. Recommend investigating patterns and potential operational bottlenecks. |
| 3.3.5 | How to **classify transaction types** based on `transType` and `merchant_id`? | Standardize transaction types for downstream analysis | Rules:<br>- transType=2 & merchant_id=1205 → Bank Transfer Transaction<br>- transType=2 & merchant_id=2260 → Withdraw Money Transaction<br>- transType=2 & merchant_id=2270 → Top Up Money Transaction<br>- transType=2 & others → Payment Transaction<br>- transType=8 & merchant_id=2250 → Transfer Money Transaction<br>- transType=8 & others → Split Bill Transaction<br>- Remaining → Invalid | Classification enables aggregation by type, facilitating analysis of customer behavior and operational performance. |
| 3.3.6 | For each transaction type (excluding invalids), what are **total transactions, volume, senders, and receivers**? | Measure activity and revenue contribution per transaction type | **Bank Transfer Transaction:** 14,004 tx, 10,061,351,762 volume, 9,271 senders/receivers<br>**Payment Transaction:** 260,335 tx, 34,385,618,147 volume, 113,298 receivers, 102,995 senders<br>**Split Bill Transaction:** 1,376 tx, 4,901,464 volume, 572 receivers, 1,323 senders<br>**Top Up Money Transaction:** 290,498 tx, 108,605,618,829 volume, 110,409 senders/receivers<br>**Transfer Money Transaction:** 341,173 tx, 37,032,880,492 volume, 34,585 receivers, 39,021 senders<br>**Withdraw Money Transaction:** 33,725 tx, 23,418,181,420 volume, 24,814 senders/receivers | Top contributors in volume are **Top Up Money** and **Payment Transactions**. Split Bill transactions are few but may indicate niche usage. Bank Transfer and Withdraw are smaller in volume but may involve higher-value transfers. Understanding distribution helps product strategy and operational focus. |


## 🎯 Conclusion
This analysis of e-wallet payments and transactions provides the following key insights:

- **Top-performing products:** A small number of products contribute the majority of payment volume. Monitoring these key products ensures efficient resource allocation and focus.

- **Team performance:** All products comply with the “1 product → 1 team” rule. The APS team shows the lowest performance since Q2 2023, with one category contributing least, highlighting areas for operational improvement.

- **Refunds:** Source_id 38 dominates refund transactions, indicating a potential area for operational attention or risk management.

- **Transaction behavior:**  
  - **Top Up Money** and **Payment transactions** account for the largest volumes.  
  - **Bank Transfer, Withdraw, and Split Bill transactions** are smaller in volume but may involve high-value or niche activity.  
  - Classifying transactions by type enables better understanding of customer behavior and team performance.

**Overall**, combining payment volume analysis, team performance assessment, and transaction-type classification provides actionable insights for:  

1. Identifying underperforming teams and product categories  
2. Vendor/product strategy optimization  
3. Refund and fraud risk monitoring  
4. Enhancing the overall customer transaction experience




