# Local ETL Lab – Build a Simple Data Pipeline

## Lab Overview

In this lab, you will build a small **ETL pipeline** that processes sales data.

Pipeline stages:

1. **Extract** – Read raw CSV files
2. **Transform** – Clean and enrich the data
3. **Load** – Store processed data in a SQLite database

Final Output:

* Clean dataset
* SQLite database with a `sales` table
* Summary metrics

---

# Lab Architecture

```
Raw Data (CSV)
      │
      ▼
Extract Script
      │
      ▼
Transformation Logic
(clean, filter, derive columns)
      │
      ▼
Load Process
      │
      ▼
SQLite Database
```

Everything runs locally.

---

# Lab Duration

60–90 minutes

---

# Prerequisites

Basic knowledge of:

* Python
* CSV files
* SQL basics

---

# Step 1 — Verify System Dependencies

Open terminal / PowerShell.

### Check Python

```bash
python --version
```

Expected:

```
Python 3.10+
```

If not installed:

Download:
[https://www.python.org/downloads/](https://www.python.org/downloads/)

Install with **Add Python to PATH** enabled.

---

### Check pip

```bash
pip --version
```

If missing:

```
python -m ensurepip --upgrade
```

---

# Step 2 — Install Required Packages

Install required Python libraries.

```bash
pip install pandas sqlalchemy
```

Verify installation:

```bash
pip list
```

You should see:

```
pandas
SQLAlchemy
```

---

# Step 3 — Create Project Structure

Create a folder:

```
etl-lab
```

Inside create:

```
etl-lab
│
├── data
│   └── sales_raw.csv
│
├── scripts
│   └── etl_pipeline.py
│
└── database
```

---

# Step 4 — Create Raw Data

Create:

```
data/sales_raw.csv
```

Add sample data:

```csv
order_id,customer,product,price,quantity,date
1001,Alice,Laptop,900,1,2024-01-10
1002,Bob,Mouse,20,2,2024-01-11
1003,Charlie,Keyboard,45,1,2024-01-12
1004,Alice,Monitor,200,1,2024-01-12
1005,Bob,Mouse,20,3,2024-01-13
1006,David,Laptop,900,1,2024-01-14
```

---

# Step 5 — Create ETL Script

Create:

```
scripts/etl_pipeline.py
```

Add the following code.

```python
import pandas as pd
from sqlalchemy import create_engine

print("Starting ETL pipeline...")

# ----------------------
# EXTRACT
# ----------------------

print("Extracting data...")

df = pd.read_csv("../data/sales_raw.csv")

print("Raw Data:")
print(df.head())

# ----------------------
# TRANSFORM
# ----------------------

print("Transforming data...")

# remove duplicates
df = df.drop_duplicates()

# convert date column
df["date"] = pd.to_datetime(df["date"])

# create total sales column
df["total_price"] = df["price"] * df["quantity"]

# filter invalid rows
df = df[df["price"] > 0]

print("Transformed Data:")
print(df.head())

# ----------------------
# LOAD
# ----------------------

print("Loading data into SQLite database...")

engine = create_engine("sqlite:///../database/sales.db")

df.to_sql(
    "sales",
    engine,
    if_exists="replace",
    index=False
)

print("Data loaded successfully!")

# ----------------------
# ANALYSIS
# ----------------------

print("Generating summary...")

summary = df.groupby("product")["total_price"].sum()

print(summary)

print("ETL Pipeline Completed.")
```

---

# Step 6 — Run the ETL Pipeline

Navigate to the scripts folder.

```
cd etl-lab/scripts
```

Run:

```bash
python etl_pipeline.py
```

Expected output:

```
Starting ETL pipeline...
Extracting data...
Transforming data...
Loading data into SQLite database...
ETL Pipeline Completed.
```

---

# Step 7 — Verify Database

A database will be created:

```
database/sales.db
```

To inspect it using Python:

```python
import sqlite3
import pandas as pd

conn = sqlite3.connect("sales.db")

df = pd.read_sql("SELECT * FROM sales", conn)

print(df)
```

---

# Step 8 — Validate Results

Expected table:

| order_id | customer | product  | price | quantity | total_price |
| -------- | -------- | -------- | ----- | -------- | ----------- |
| 1001     | Alice    | Laptop   | 900   | 1        | 900         |
| 1002     | Bob      | Mouse    | 20    | 2        | 40          |
| 1003     | Charlie  | Keyboard | 45    | 1        | 45          |

---

# Step 9 — Add Data Quality Checks

Add validation before loading.

Example:

```python
if df.isnull().sum().sum() > 0:
    print("Warning: Null values detected")
```

---

# Step 10 — Extend the Lab

Students can improve the pipeline.

Ideas:

### Add Logging

```
pip install loguru
```

---

### Schedule the ETL

Run with cron / task scheduler.

Linux:

```
crontab -e
```

---

### Add Multiple Data Sources

Add:

```
sales_january.csv
sales_february.csv
```

Then merge.

---

### Add Visualization

Install:

```
pip install matplotlib
```

Plot revenue trends.

---

# Final Learning Outcomes

Students will understand:

* ETL architecture
* Data extraction
* Data cleaning
* Data transformation
* Loading into databases
* Data validation
* Pipeline automation
