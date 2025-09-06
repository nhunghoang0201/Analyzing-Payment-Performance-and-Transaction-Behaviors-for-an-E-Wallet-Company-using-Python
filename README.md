# 📊 Analyzing Payment Performance and Transaction Behaviors for an E-Wallet Company using Python  
Author: Hoang Thi Hong Nhung 
Date: 2025-09-03  
Tools Used: Python

## 📑 Table of Contents  
1. [📌 Background & Overview](#-background--overview)  
2. [📂 Dataset Description & Data Structure](#-dataset-description--data-structure)  
3. [🔎 Final Conclusion & Recommendations](#-final-conclusion--recommendations)

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
| Column Name | Data Type | Description                          |
| ----------- | --------- | ------------------------------------ |
| product\_id | INT       | Unique identifier for each product   |
| category    | TEXT      | Product category (e.g., PXXXXXB)     |
| team\_own   | TEXT      | The team responsible for the product |
Example:
| product\_id | category | team\_own |
| ----------- | -------- | --------- |
| 17          | PXXXXXB  | ASD       |
| 18          | PXXXXXB  | ASD       |
| 20          | PXXXXXB  | ASD       |
##### Table 2: Payment Report Table
Payment Report Table
| Column Name    | Data Type | Description                               |
| -------------- | --------- | ----------------------------------------- |
| report\_month  | DATE      | Month of report (YYYY-MM)                 |
| payment\_group | TEXT      | Type of payment (e.g., payment, refund)   |
| product\_id    | INT       | Reference to product\_id in Product Table |
| source\_id     | INT       | Source identifier of payment              |
| volume         | FLOAT     | Total payment volume                      |
Example
| report\_month | payment\_group | product\_id | source\_id | volume    |
| ------------- | -------------- | ----------- | ---------- | --------- |
| 2023-01       | payment        | 12          | 45         | 624110375 |
| 2023-01       | payment        | 17          | 45         | 335715113 |

##### Table 3: Transactions Table
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
Example:
| transaction\_id | merchant\_id | volume | transType | transStatus | sender\_id | receiver\_id | timeStamp     |
| --------------- | ------------ | ------ | --------- | ----------- | ---------- | ------------ | ------------- |
| 3002692434      | 5            | 100000 | 24        | 1           | 10199794   | 199794       | 1682932054455 |
| 3002692437      | 305          | 20000  | 2         | 1           | 14022211   | 14022211     | 1682932054912 |

## ⚒️ Main Process
#### 1️⃣ Exploratory Data Analysis (EDA)  

#### 2️⃣ Data Cleaning & Preprocessing
**Payment_enriched**

*1. Missing data:*
- category: 22 null values => Change to others
- team_own: 22 null values => Change to others

*2. incorrect data types*
- report_month (type: object) => Action: Change to datetime
- source_id (type: int64) => Action: Change to strings
- product_id (type: int64) => Action: Change to strings

**Transactions**

*1. Duplicate data*
- transaction_id => Action: Drop duplicate data

*2. Missing data:*
- sender_id: 49059 null values => No Action as senders could be already deleted in system
- receiver_id: 164795 null values => Action: Delete rows with receiver_id null as receiver information could be
- extra_info: 1317907 null values => No Action

*3. Incorrect data types*
- transaction_id (type: int64) => Action: Change to strings
- merchant_id (type: int64) => Action: Change to strings
- transType (type: int64) => Action: Change to strings
- transStatus (type: int64) => Action: Change to strings
- sender_id (type: float64) => Action: Change to strings
- receiver_id (type: float64) => Action: Change to strings
- timestamp (type: object) => Action: Change to datetime

#### 3️⃣ Data wrangling
<img width="1778" height="743" alt="image" src="https://github.com/user-attachments/assets/2f19a0fd-202d-4c25-a6d1-1151b98cbdcf" />
<img width="1329" height="270" alt="image" src="https://github.com/user-attachments/assets/b991217f-bf69-47ef-a618-e25df41eb094" />
<img width="1083" height="860" alt="image" src="https://github.com/user-attachments/assets/a5865189-cddb-437b-8c05-0ce41b10fdd6" />
<img width="1816" height="456" alt="image" src="https://github.com/user-attachments/assets/b651dbfa-a38d-425c-8126-85142c375e78" />



