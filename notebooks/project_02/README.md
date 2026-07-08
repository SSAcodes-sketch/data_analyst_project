# Transaction Data Cleaning & Pipeline Automation

## 📌 Project Overview
This repository contains a robust, end-to-end data cleaning pipeline developed in Python using the **Pandas** library. The project transforms a chaotic, production-logged transaction dataset containing systemic string artifacts, missing timestamps, and categorical anomalies into a clean, normalized, and analytical-ready dataset.

The primary objective was to enforce strict data integrity, correct structural mutations, eliminate data formatting discrepancies, and drop system errors without compromising valid observational units.

---

## 🛠️ Tech Stack & Library Framework
* **Language:** Python 3.x
* **Core Library:** Pandas (High-performance data structures and data analysis tool)
* **Mathematical Utilities:** NumPy (Vectorized array operations and numerical missing value placeholders)
* **Environment:** Jupyter Notebook / Anaconda

---

## 🧼 Data Cleaning Workflow & Architecture

The pipeline systematically addresses engineering anomalies across critical data dimensions:

### 1. Categorical Standardization (`Payment Method`)
* **Problem:** Over 100 unique text variations introduced via upstream system errors (`ERROR`, `UNKNOWN`, structural noise).
* **Solution:** * Isolated valid domain values (`['Digital Wallet', 'Cash', 'Credit Card']`).
  * Utilized vectorized boolean masks (`~df.isin()`) and exact-value map overrides to safely transition multi-category structural noise into formal `np.nan` missing-value markers.
  * Extracted hidden missing data by resolving default Pandas `.value_counts()` structural omissions using `dropna=False` validation parameters.
  * Formally purged the system ghosts from memory using subset-isolated drops (`.dropna(subset=['Payment Method'])`).

### 2. Temporal Type Casting (`Transaction Date`)
* **Problem:** Dates were locked as immutable string/object (`str`) data types, breaking chronological sorting and future time-series feature engineering.
* **Solution:** * Enforced datetime normalization utilizing `pd.to_datetime()`.
  * Employed `errors='coerce'` as a safe failure-mode parsing strategy, converting non-standard text discrepancies into native `NaT` (Not-a-Time) missing values.
  * Dropped structural temporal failures to guarantee a pure `datetime64[ns]` array schema across remaining observations.

### 3. Unique Entity Integrity (`Transaction ID`)
* **Problem:** Text identifiers vulnerable to trailing/leading white space collisions, alphanumeric case fragmentation, and structural system duplications.
* **Solution:**
  * Implemented an evaluation pattern to strip invisible leading/trailing whitespace buffers (`.str.strip()`) and unified casing to standard uppercase formats (`.str.upper()`).
  * Structured multi-dimensional constraint filters using continuous Regular Expressions (`regex=True` pattern matching: `r'[^A-Z0-9]'`) to isolate and intercept rogue special symbols or formatting failures.
  * Verified structural entity uniqueness using dual boolean mask duplications (`.duplicated(keep=False)`), completely ensuring no computational overlap exists inside the identifier index.

---

## 📈 Final Dataset Integrity Profile
The final pipeline verification yields a flawless, validated runtime environment profile:

* **Missing-Value Index:** 0% `NaN` / `NaT` density across core feature columns.
* **Unique Key Validation:** Duplicate Transaction IDs return strict `False`.
* **Feature Schema Alignment:** * `Transaction ID` $\rightarrow$ Standardized Alphanumeric String (`object`)
  * `Payment Method` $\rightarrow$ Strict 3-Category Nominal String (`object`)
  * `Transaction Date` $\rightarrow$ Chronological Timestamp (`datetime64[ns]`)

---

## 🚀 How to Execute the Pipeline

### Prerequisites
Ensure your local Python environment has the required dependencies deployed:
```bash
pip install pandas numpy

import pandas as pd
import numpy as np

# Load raw system extraction
df = pd.read_csv('your_raw_transactions.csv')
df_clear = df.copy()

# Node 1: Clean Categorical Attributes
valid_payments = ['Digital Wallet', 'Cash', 'Credit Card']
df_clear.loc[~df_clear['Payment Method'].isin(valid_payments), 'Payment Method'] = np.nan
df_clear = df_clear.dropna(subset=['Payment Method'])

# Node 2: Transform Temporal Attributes
df_clear['Transaction Date'] = pd.to_datetime(df_clear['Transaction Date'], errors='coerce')
df_clear = df_clear.dropna(subset=['Transaction Date'])

# Node 3: Normalize Entity Identifiers
df_clear['Transaction ID'] = df_clear['Transaction ID'].astype(str).str.strip().str.upper()
# Drop lengths or regex character exceptions if necessary
df_clear = df_clear.drop_duplicates(subset=['Transaction ID'])

# Export clean target asset
df_clear.to_csv('cleaned_transactions_data.csv', index=False)
print("🚀 Clean CSV successfully generated!")