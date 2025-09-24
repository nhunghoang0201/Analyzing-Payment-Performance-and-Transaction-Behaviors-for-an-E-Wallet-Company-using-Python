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
#### 🎯 Situation

The e-wallet company is operating multiple products and transaction types: **Bank Transfer**, **Withdraw Money**, **Top Up Money**, **Payment Transaction**, **Transfer Money**, and **Split Bill**.

#### 📂 Complication / Business Questions
The current status of payments and transactions is unclear, making it difficult to evaluate performance and support decision-making:

✔️ **Product/Team**: Which products contribute the most? Which team performs the weakest? Are there any ownership violations?

✔️ **Refund**: Which source_id contributes the most to refunds?

✔️ **Transaction**: Is the data clean? Which transaction types are most common, and what are their volumes and user counts?

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
</details>
  
<details>
  <summary>Example:</summary>
  
| product\_id | category | team\_own |
| ----------- | -------- | --------- |
| 17          | PXXXXXB  | ASD       |
| 18          | PXXXXXB  | ASD       |
| 20          | PXXXXXB  | ASD       |
</details>
  
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
</details>

<details>
  <summary>Example</summary>
  
| report\_month | payment\_group | product\_id | source\_id | volume    |
| ------------- | -------------- | ----------- | ---------- | --------- |
| 2023-01       | payment        | 12          | 45         | 624110375 |
| 2023-01       | payment        | 17          | 45         | 335715113 |
</details>

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
</details>

<details>
  <summary>Example:</summary>
  
| transaction\_id | merchant\_id | volume | transType | transStatus | sender\_id | receiver\_id | timeStamp     |
| --------------- | ------------ | ------ | --------- | ----------- | ---------- | ------------ | ------------- |
| 3002692434      | 5            | 100000 | 24        | 1           | 10199794   | 199794       | 1682932054455 |
| 3002692437      | 305          | 20000  | 2         | 1           | 14022211   | 14022211     | 1682932054912 |
</details>
  
## ⚒️ Main Process
#### 3.1. Exploratory Data Analysis (EDA)  

#### 3.2. Data Cleaning & Preprocessing
**Payment_enriched**

| Findings                                             | Solution                                      | Result / Data Readiness                                                |
|------------------------------------------------------|-----------------------------------------------|------------------------------------------------------------------------|
| Missing values: `category` (22 nulls)                | Fill nulls with "others"                      | ✅ All rows now have valid category values                             |
| Missing values: `team_own` (22 nulls)                | Fill nulls with "others"                      | ✅ All rows now have valid team ownership values                       |
| Wrong data type: `report_month` (object)             | Convert to datetime                           | ✅ Converted → ready for time-series analysis                          |
| Wrong data type: `source_id` (int64)                 | Convert to string                             | ✅ Converted → IDs consistent, treated as categorical                  |
| Wrong data type: `product_id` (int64)                | Convert to string                             | ✅ Converted → IDs consistent, treated as categorical                  |

---
**Transactions**
| Findings                                             | Solution                                      | Result / Data Readiness                                                |
|------------------------------------------------------|-----------------------------------------------|------------------------------------------------------------------------|
| Duplicate rows in `transaction_id`                   | Drop duplicate rows                           | ✅ Ensures each transaction is unique                                  |
| Missing values: `sender_id` (49,059 nulls)           | No Action → senders may have been deleted     | ⚠️ Nulls remain but acceptable, will not block transaction analysis   |
| Missing values: `receiver_id` (164,795 nulls)        | Delete rows with null `receiver_id`           | ✅ All remaining rows have valid receiver information                  |
| Missing values: `extra_info` (1,317,907 nulls)       | No Action → considered non-critical metadata  | ⚠️ Column incomplete but not blocking payment/transaction analysis     |
| Wrong data type: `transaction_id` (int64)            | Convert to string                             | ✅ Converted → IDs consistent as categorical                           |
| Wrong data type: `merchant_id` (int64)               | Convert to string                             | ✅ Converted → IDs consistent as categorical                           |
| Wrong data type: `transType` (int64)                 | Convert to string                             | ✅ Converted → avoids misinterpretation in classification              |
| Wrong data type: `transStatus` (int64)               | Convert to string                             | ✅ Converted → ready for label-based analysis                          |
| Wrong data type: `sender_id` (float64)               | Convert to string                             | ✅ Converted → IDs consistent, avoids decimal formatting               |
| Wrong data type: `receiver_id` (float64)             | Convert to string                             | ✅ Converted → IDs consistent, avoids decimal formatting               |
| Wrong data type: `timestamp` (object)                | Convert to datetime                           | ✅ Converted → enables time-series and trend analysis                  |

#### 3️.3. Data wrangling
| # | Business Question | Purpose | Answer | Analysis / Insight |
|---|---------|---------|--------|------------------|
| 3.3.1 | Which **3 product_ids** have the highest payment volume? | Identify top-performing products by revenue | 1976: 61,797,583,647<br>429: 14,667,676,567<br>372: 13,713,658,515 | These products generate the majority of payment volume. Focus on their operational efficiency and monitoring. |
| 3.3.2 | Are there any products that are **owned by multiple teams**? | Ensure **1 product → 1 team** rule is maintained | No | All products comply with ownership rules, ensuring accountability and simplifying performance tracking. |
| 3.3.3 | Which **team has the lowest performance** since Q2 2023? Which category contributes least to that team? | Identify underperforming teams and weak product categories for targeted improvement | Team: APS<br>Lowest contributing category: 25,232,438 | APS team may need operational support or process improvement. Focus on the low-contributing category to boost overall performance. |
| 3.3.4 | Among **refund transactions**, which **source_id** contributes the most? | Understand refund sources for risk/fraud monitoring | Source_id: 38 | This source accounts for the highest refund volume. Recommend investigating patterns and potential operational bottlenecks. |
| 3.3.5 | How to **classify transaction types** based on `transType` and `merchant_id`? | Standardize transaction types for downstream analysis | Rules:<br>- transType=2 & merchant_id=1205 → Bank Transfer Transaction<br>- transType=2 & merchant_id=2260 → Withdraw Money Transaction<br>- transType=2 & merchant_id=2270 → Top Up Money Transaction<br>- transType=2 & others → Payment Transaction<br>- transType=8 & merchant_id=2250 → Transfer Money Transaction<br>- transType=8 & others → Split Bill Transaction<br>- Remaining → Invalid | Classification enables aggregation by type, facilitating analysis of customer behavior and operational performance. |
| 3.3.6 | For each transaction type (excluding invalids), what are **total transactions, volume, senders, and receivers**? | Measure activity and revenue contribution per transaction type | **Bank Transfer Transaction:** 14,004 tx, 10,061,351,762 volume, 9,271 senders/receivers<br>**Payment Transaction:** 260,335 tx, 34,385,618,147 volume, 113,298 receivers, 102,995 senders<br>**Split Bill Transaction:** 1,376 tx, 4,901,464 volume, 572 receivers, 1,323 senders<br>**Top Up Money Transaction:** 290,498 tx, 108,605,618,829 volume, 110,409 senders/receivers<br>**Transfer Money Transaction:** 341,173 tx, 37,032,880,492 volume, 34,585 receivers, 39,021 senders<br>**Withdraw Money Transaction:** 33,725 tx, 23,418,181,420 volume, 24,814 senders/receivers | Top contributors in volume are **Top Up Money** and **Payment Transactions**. Split Bill transactions are few but may indicate niche usage. Bank Transfer and Withdraw are smaller in volume but may involve higher-value transfers. Understanding distribution helps product strategy and operational focus. |

#### 3.3.1 Top 3 Products by Volume
**Purpose:** Identify which products generate the highest payment volume.  This helps prioritize operational focus, marketing resources
**Code**:
<img width="911" height="148" alt="image" src="https://github.com/user-attachments/assets/8ec60406-dbad-4cbc-b595-36e840596da0" />

**Answer:**  Top 3 Products is 1976, 429, 372.
**Insight:**  
- We found that only **3 products** (IDs: 1976, 429, 372) make up the **majority of total payment volume**.
  
- In other words, almost all the money comes from just a few products.
  
- 👉 Why this matters: If one of these products has a problem (system error, fraud, downtime), the company could lose a lot of money quickly.
   
- ✅ What to do: Keep these products running smoothly, monitor them daily, and maybe create backup plans.

---

#### 3.3.2 Ownership Rule Check
**Purpose:** Verify that each product is owned by exactly one team. This ensures accountability, avoids duplicated responsibility, and makes performance evaluation transparent.

**Code:** 
<img width="751" height="198" alt="image" src="https://github.com/user-attachments/assets/e59bf5be-8ed9-4251-a222-a31b42bbe7b2" />

**Answer:** As all product is own by only one team => No violations  
**Insight:** All products comply, ensuring accountability and easy tracking.  

---

#### 3.3.3 Lowest Performing Team
**Purpose:** Detect which team contributes the least to transaction volume. This highlights where management may need to allocate support, training, or process improvements.

**Code:** 
<img width="1405" height="250" alt="image" src="https://github.com/user-attachments/assets/f612104f-a377-40db-bcdd-41400471a317" />

**Answer:**  
- Team: APS  
- Lowest category contribution: 25,232,438  

**Insight:**   
- Since Q2 2023, the **APS team has the lowest performance** (they generate the least payment volume).  
- 👉 Why this matters: This could mean their products are not popular, not marketed well, or they have internal issues.  
- ✅ What to do: Review APS team’s products. Maybe give them more resources, training, or marketing support.  

---

#### 3.3.4 Refund Analysis
**Purpose:** Understand refund sources for risk/fraud monitoring  

**Code:**
<img width="869" height="201" alt="image" src="https://github.com/user-attachments/assets/9bcc1f2b-6777-416c-98cf-cb08a7a5dadc" />

**Answer:** Source_id = 38  

**Insight:** 
- We saw that **source_id = 38** is responsible for most of the **refund transactions**.  
- 👉 Why this matters: Too many refunds can mean fraud, unhappy customers, or system bugs. If we don’t fix it, trust in the wallet can drop.  
- ✅ What to do: Investigate source 38 in detail. Check if it’s a risky merchant, a system issue, or a process problem.  


---

#### 3.3.5 Transaction Classification
**Rules:**  
- `transType=2 & merchant_id=1205` → Bank Transfer  
- `transType=2 & merchant_id=2260` → Withdraw Money  
- `transType=2 & merchant_id=2270` → Top Up Money  
- `transType=2 & others` → Payment Transaction  
- `transType=8 & merchant_id=2250` → Transfer Money  
- `transType=8 & others` → Split Bill  
- Else → Invalid
- 
**Purpose:** Standardize transaction types. This allows for consistent reporting, easier aggregation, and better understanding of user behaviors.
  
**Code:**
<img width="1072" height="722" alt="image" src="https://github.com/user-attachments/assets/cd8a9624-5481-4518-9622-290e9bf1d001" />

**Insight:** 
- We created rules to classify each transaction into categories: **Top Up, Payment, Withdraw, Bank Transfer, Transfer Money, Split Bill, or Invalid**.  
- 👉 Why this matters: Before, it was messy and hard to analyze. Now we can group and compare transaction types easily.  
- ✅ What to do: Automate these rules so every new transaction is classified correctly in real time.  


---

#### 3.3.6 Transaction Type Summary
**Purpose:** Measure how different transaction types contribute to overall system usage  

**Code**: 
<img width="1163" height="401" alt="image" src="https://github.com/user-attachments/assets/ac843c32-dbd4-41ab-90d4-f3eea045bb65" />

**Insight:**  
- **Top Up Money** and **Payments** are the biggest activities → these are the **core services** people use daily.  
- **Transfer Money** is also strong → shows people send money to each other often.  
- **Split Bill** is small → not many people use it, but it’s a **unique social feature** that could grow.  
- **Withdraw / Bank Transfer** are smaller in number, but they usually involve **bigger amounts of money**.  

👉 Why this matters:  
- The company’s strength is in **Top Up + Payment** → focus on growing these.  
- **Transfer** shows trust in the wallet for P2P.  
- **Split Bill** could be a niche feature to promote.  
- **Withdraw / Bank Transfer** affect liquidity, so they need careful monitoring.  

## 🎯 Conclusion
This analysis of e-wallet payments and transactions provides the following key insights:

### Top Products Drive Most Value
## ✅ The Big Picture
- The wallet makes most of its money from a **few key products and services**.  
- Ownership is well-structured (good), but one team (**APS**) is falling behind (bad).  
- Refunds are heavily concentrated in **one source (38)**, which could be a risk.  
- Customers mainly use the wallet for **Top Up and Payments**, but also use it to **transfer money** and sometimes **split bills**.  

👉 To improve the business:  
1. **Protect the top products** – keep them running perfectly.  
2. **Fix refund issues** – start with source 38.  
3. **Support APS team** – help them improve their performance.  
4. **Grow usage** – focus on core services and test growth ideas for Split Bill.  




