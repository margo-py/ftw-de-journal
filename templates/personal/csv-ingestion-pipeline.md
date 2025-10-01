
# 📝 Instructions: Ingest CSV Files into a Data Pipeline (Beginner-Friendly)

This guide shows how to create a simple **ETL pipeline** using [dlt](https://dlthub.com/) that loads CSV files from your **local computer** into a database.

We’ll use an **example dataset about an online shop’s sales**.

---

## 1. Folder Setup

Create a project folder like this:

```
my_pipeline/
  pipeline.py
  staging/
    online_shop/
      customers.csv
      orders.csv
      products.csv
```

* `pipeline.py` → your ingestion script
* `staging/online_shop/` → contains your CSV files

---

## 2. Example CSV Files

Let’s say you have:

**customers.csv**

```csv
customer_id,name,email
1,Alice,alice@example.com
2,Bob,bob@example.com
```

**orders.csv**

```csv
order_id,customer_id,product_id,quantity
1001,1,2001,2
1002,2,2002,1
```

**products.csv**

```csv
product_id,product_name,price
2001,Laptop,1200
2002,Mouse,25
```

---

## 3. Pipeline Script (`pipeline.py`)

```python
import dlt
import pandas as pd
import os

def load_csv_resources(dataset_folder: str):
    """Create a DLT resource for each CSV file in staging/{dataset_folder}/"""
    ROOT_DIR = os.path.dirname(__file__)
    DATASET_DIR = os.path.join(ROOT_DIR, "staging", dataset_folder)

    for filename in os.listdir(DATASET_DIR):
        if filename.endswith(".csv"):
            table_name = os.path.splitext(filename)[0]  # e.g. "orders.csv" -> "orders"

            @dlt.resource(name=table_name, write_disposition="replace")
            def resource_loader(file_path=os.path.join(DATASET_DIR, filename)):
                yield pd.read_csv(file_path)

            yield resource_loader()

def run_pipeline():
    """Run the pipeline to load all CSVs from the 'online_shop' dataset"""
    p = dlt.pipeline(
        pipeline_name="online_shop_pipeline",
        destination="duckdb",      # You can replace with 'postgres', 'bigquery', etc.
        dataset_name="online_shop" # Logical grouping of your tables
    )

    print("Loading CSV files from staging/online_shop/...")

    info = p.run(list(load_csv_resources("online_shop")))
    print("Records loaded:", info)

if __name__ == "__main__":
    run_pipeline()
```

---

## 4. Run the Pipeline

From your terminal:

```bash
cd my_pipeline
python pipeline.py
```

---

## 5. What Happens

* Each CSV file in `staging/online_shop/` becomes a **table**:

  * `customers.csv` → table `customers`
  * `orders.csv` → table `orders`
  * `products.csv` → table `products`
* All tables are grouped into a dataset called **online_shop**.
* Data is loaded into the destination you choose (default in example: **DuckDB**, a simple local database).

---

✅ That’s it — you now have a working ingestion pipeline that loads your local CSVs into a database.

---
