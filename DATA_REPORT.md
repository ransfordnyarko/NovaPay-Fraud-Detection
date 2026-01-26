# NovaPay Dataset Report

This document is a data assessment report summarizing the data scope and assessing the imbalance and bias risks

---

## Data Scope

The NovaPay dataset contains 11,400 cross-border transactions primarily across the United States, Canada and United Kingdom processed by NovaPay. Each record represents a single transaction and contains transaction metadata, customer attributes, and risk indicator. This data would be relied on to build a fraud detection model. Find below a data dictionary to explain in detail the data collected for each record

---

## NovaPay Dataset Dictionary

## Data Dictionary

### Transaction Metadata

| Column Name               | Data Type            | Description                                                       |
| ------------------------- | -------------------- | ----------------------------------------------------------------- |
| transaction_id            | string (identifier)  | Unique identifier for each transaction                            |
| channel                   | categorical (string) | Channel used to initiate the transaction (e.g., ATM, mobile, web) |
| source_currency           | categorical (string) | Currency in which the transaction was initiated                   |
| dest_currency             | categorical (string) | Currency received by the beneficiary                              |
| amount_src                | numeric (float)      | Transaction amount in the source currency                         |
| amount_usd                | numeric (float)      | Transaction amount normalized to USD                              |
| fee                       | numeric (float)      | Fee charged for processing the transaction                        |
| exchange_rate_src_to_dest | numeric (float)      | Exchange rate applied for currency conversion                     |

### Temporal & Behavioral Features

| Column Name      | Data Type         | Description                                           |
| ---------------- | ----------------- | ----------------------------------------------------- |
| timestamp        | datetime          | Time at which the transaction was initiated           |
| txn_velocity_1h  | numeric (integer) | Number of transactions initiated in the past hour     |
| txn_velocity_24h | numeric (integer) | Number of transactions initiated in the past 24 hours |

### Customer Data

| Column Name              | Data Type            | Description                                       |
| ------------------------ | -------------------- | ------------------------------------------------- |
| customer_id              | string (identifier)  | Unique identifier for the customer                |
| home_country             | categorical (string) | Customer’s registered country of residence        |
| kyc_tier                 | categorical (string) | Customer verification level                       |
| account_age_days         | numeric (integer)    | Days since the customer account was created       |
| chargeback_history_count | numeric (integer)    | Number of historical chargebacks for the customer |

### Device & Network Signals

| Column Name        | Data Type            | Description                                            |
| ------------------ | -------------------- | ------------------------------------------------------ |
| device_id          | string (identifier)  | Identifier for the device used                         |
| new_device         | boolean              | Indicates whether the device is new for the customer   |
| device_trust_score | numeric (float)      | Trust score assigned to the device                     |
| ip_address         | string (identifier)  | IP address used for the transaction                    |
| ip_country         | categorical (string) | Country inferred from the IP address                   |
| location_mismatch  | boolean              | Indicates mismatch between IP country and home country |
| ip_risk_score      | numeric (float)      | Risk score associated with the IP address              |

### Risk & Compliance Signals

| Column Name         | Data Type       | Description                                                |
| ------------------- | --------------- | ---------------------------------------------------------- |
| corridor_risk       | numeric (float) | Risk score associated with the source–destination corridor |
| risk_score_internal | numeric (float) | Internal risk score generated prior to transaction         |

### Target Variable

| Column Name | Data Type       | Description                                          |
| ----------- | --------------- | ---------------------------------------------------- |
| is_fraud    | binary (target) | Indicates whether the transaction is confirmed fraud |

---

## NovaPay Dataset Quality Findings and Class Imbalance

An initial data quality assessment identified multiple issues across categorical, continuous, and enrichment variables that require attention prior to modeling. Categorical variables showed significant normalization problems. In `home_country`, multiple representations of the same country were observed due to inconsistent casing and trailing whitespace (e.g., `"US"`, `" US "`, `"UK"`, `" UK "`), along with placeholder values such as `"unknown"`. The `channel` variable exhibited even greater inconsistency, including variations in casing, spacing, and spelling (e.g., `"web"`, `"WEB"`, `"web "`, `"ATM"`, `"ATm"`, `"weeb"`, `"mobile "`), indicating logging or ingestion issues that would inflate cardinality and distort category-level fraud signals if left uncorrected. The `kyc_tier` field contained mixed casing, trailing spaces, misspellings (e.g., `"standrd"`), explicit string encodings of missing values (e.g., `"NAN"`, `" nan "`), and true nulls, all of which need to be consolidated into a controlled and semantically meaningful set of categories. \

Continuous variables also revealed values that are implausible from a business and logical standpoint. The `fee` variable contained negative values, which are inconsistent with expected transaction fee behavior, as well as extreme outliers with values approaching 10,000, suggesting potential adjustment artifacts, special-case transactions, or data entry errors. The `device_trust_score`, which is expected to fall within a bounded range between 0 and 1, included negative values, indicating possible scaling errors or issues in upstream score generation. Similarly, the `txn_velocity_1h` feature, which represents a transaction count, contained negative values despite counts being inherently non-negative, pointing to potential preprocessing or aggregation logic errors. These findings indicate that while extreme values should not be automatically removed in a fraud context, several fields require validation and correction to ensure semantic correctness.

An assessment of missing values showed that core identifiers and primary transaction metadata are largely complete; however, missingness is concentrated in enrichment and auxiliary risk features. Approximately 300 records are missing values for `amount_usd`, `ip_address`, `ip_country`, `kyc_tier`, `fee`, and `device_trust_score`, suggesting incomplete coverage from currency normalization, network attribution, KYC processes, or device reputation systems. The `timestamp` field also contains a small number of missing values, which is notable given its importance for temporal and velocity-based features and may require row-level handling. This pattern of missingness appears systematic rather than random and should be explicitly modeled or encoded rather than naively imputed.

Finally, the target variable exhibits moderate class imbalance, with approximately 8.7% of transactions labeled as fraudulent and 91.3% labeled as legitimate. While this imbalance is typical in fraud detection datasets, it has direct implications for model training and evaluation, necessitating the use of metrics such as precision, recall, and area under the precision–recall curve rather than accuracy alone. Overall, the dataset is suitable for fraud modeling, but the identified data quality issues highlight the need for targeted normalization, validation, and missing-value strategies before proceeding to feature engineering and model development.
