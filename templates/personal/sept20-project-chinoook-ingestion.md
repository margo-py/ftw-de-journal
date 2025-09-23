# Setting Up a Data Pipeline with DLT and DBT

## Project Goal

The goal of this project was to build a robust data pipeline that extracts data from the Chinook database, transforms it using modern data tools, and loads it into ClickHouse for analytics. The main objective was to "consistently store efficient data that is trustable" - establishing a reliable foundation for data analysis.

## Implementation Steps

### 1. Setting Up Database Connection

First, I established a connection to the database server using DBeaver:

- Opened DBeaver and created a new ClickHouse connection
- Configured connection with the following parameters:
    - Host IP: 54.87.106.52 
    - Username: chinook
    - Password: chinook

![img](https://i.imgur.com/CsTo79i.png[/img])

- Created new credentials for the pipeline:
    - Username: ftw_user
    - Password: ftw_pass

### 2. Configuring DLT Pipeline

I personalized the DLT pipeline to include my name in the resource naming convention:

- Modified the resource definition in the MPG pipeline script:
    
    ```python
    @dlt.resource(name="cars_mikay")
    ```
    
- Set up environment variables for the ClickHouse destination:
    
    ```
    DESTINATION__CLICKHOUSE__CREDENTIALS__HOST: "<SERVER_IP>"
    DESTINATION__CLICKHOUSE__CREDENTIALS__PORT: "9000"
    DESTINATION__CLICKHOUSE__CREDENTIALS__HTTP_PORT: "8123"
    DESTINATION__CLICKHOUSE__CREDENTIALS__USERNAME: "ftw_user"
    DESTINATION__CLICKHOUSE__CREDENTIALS__PASSWORD: "ftw_pass"
    DESTINATION__CLICKHOUSE__CREDENTIALS__DATABASE: "raw"
    ```
    
- Set up environment variables for DBT:
    
    ```
    CH_HTTP_PORT: "8123"
    CH_TCP_PORT: "9000"
    CH_USER: "ftw_user"
    CH_PASS: "ftw_pass"
    ```
    

### 3. Running the DLT Pipeline

I executed the DLT job using Docker:

```
docker compose --profile jobs run --rm dlt python extract-loads/[01-dlt-mpg-pipeline.py](http://01-dlt-mpg-pipeline.py)
```

For the Chinook data pipeline, I made the following modifications:

- Updated the run definition to use my personalized dataset name:
    
    ```python
    def run():
        pipeline = dlt.pipeline(
            pipeline_name="02-dlt-chinook-pipeline",
            destination="clickhouse",
            dataset_name="chinook_mikay",
            dev_mode=False   
        )
    ```
    
- Set up the full run definition to extract all required tables:
    
    ```python
    def run():
        pipeline = dlt.pipeline(
            pipeline_name="02-dlt-chinook-pipeline",
            destination="clickhouse",
            dataset_name="chinook_mikay",
            dev_mode=False   # set True if you want DLT to drop & recreate tables on each run
        )
        load_info = [pipeline.run](http://pipeline.run)(artists())        # #1 artist
        print("records loaded:", load_info)
        load_info = [pipeline.run](http://pipeline.run)(albums())         # #2 album  
        print("records loaded:", load_info)
        load_info = [pipeline.run](http://pipeline.run)(customer())       # #3 customer
        print("records loaded:", load_info)
        load_info = [pipeline.run](http://pipeline.run)(invoice())        # #4 invoice
        print("records loaded:", load_info)
        load_info = [pipeline.run](http://pipeline.run)(invoice_line())   # #5 invoice_line
        print("records loaded:", load_info)
        load_info = [pipeline.run](http://pipeline.run)(employee())       # #6 employee
        print("records loaded:", load_info)
        load_info = [pipeline.run](http://pipeline.run)(genre())          # #7 genre 
        print("records loaded:", load_info)
        load_info = [pipeline.run](http://pipeline.run)(track())          # #8 track 
        print("records loaded:", load_info)
    ```
    
- Configured specific resources with appropriate write disposition:
    
    ```python
    @dlt.resource(write_disposition="append", name="albums_mikay")
    ```
    

### 4. Running DBT

After setting up the DLT pipeline, I ran the DBT transformations:

```
docker compose --profile jobs run --rm -w /workdir/transforms/02_chinook dbt build --profiles-dir . --target remote
```

- Made sure the naming conventions were correct for the content of dbt clean folder and dbt mart
- Ensured all target tables followed the naming structure: `database.tablename_studentname`

### 5. Creating Analytics Tables

Finally, I created analytics tables in the sandbox schema by executing SQL in DBeaver:

1. Right-clicked on the connection "Remote_FTW" in DBeaver
2. Selected SQL Editor > New SQL script
3. Executed the following SQL to create a customer dimension table:

```sql
DROP TABLE IF EXISTS sandbox.DimCustomer_mikay;
CREATE TABLE sandbox.DimCustomer_mikay
ENGINE = MergeTree
ORDER BY tuple()
AS
SELECT
    customer_id   AS customer_key,
    first_name,
    last_name,
    country
FROM raw.chinook_mikay___customer_mikay;
```

1. Created a fact table for invoice lines:

```sql
DROP TABLE IF EXISTS sandbox.MikayFactInvoiceLine;
CREATE TABLE sandbox.FactInvoiceLine_mikay
ENGINE = MergeTree
ORDER BY tuple()
AS
SELECT
    il.invoice_line_id AS invoice_line_key,
    c.customer_id     AS customer_key,
    t.track_id        AS track_key,
    i.invoice_date AS invoice_date,
    il.quantity AS quantity,
    il.unit_price * il.quantity AS line_amount
FROM raw.chinook_mikay___invoice_line_mikay il
JOIN raw.chinook_mikay___invoice_mikay i   ON il.invoice_id = i.invoice_id
JOIN raw.chinook_mikay___customer_mikay c  ON i.customer_id = c.customer_id
JOIN raw.chinook_mikay___track_mikay t     ON il.track_id = t.track_id;
```

### 6. Accessing Data Visualization

Data visualization was available through Metabase at:

http://54.87.106.52:3001/browse/databases

![Database Schema](https://i.imgur.com/ItRhvHh.png)

## Summary

This project successfully implemented a complete data pipeline that extracts data from the Chinook database, transforms it using DLT and DBT, and loads it into ClickHouse for analytics. The pipeline follows best practices for data engineering, ensuring data is properly organized, efficiently stored, and trustworthy for downstream analysis.