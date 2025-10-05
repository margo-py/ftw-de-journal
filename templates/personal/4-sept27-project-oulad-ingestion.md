# Setting Up a Data Pipeline with DLT and DBT for OULAD Dataset

## Project Goal

The goal of this project is to build a robust data pipeline that extracts data from the OULAD (Open University Learning Analytics Dataset), transforms it using modern data tools, and loads it into ClickHouse for analytics. The main objective is to "consistently store efficient data that is trustable" - establishing a reliable foundation for data analysis.

Dataset Source: https://archive.ics.uci.edu/dataset/349/open+university+learning+analytics+dataset

## Data Preparation

### 1. Download the OULAD Dataset

Before setting up the pipeline, download the dataset from the source:

1. Visit: https://archive.ics.uci.edu/dataset/349/open+university+learning+analytics+dataset
2. Download the ZIP file containing all CSV files
3. Extract the ZIP file to the `extract-loads/staging/oulad/` directory

### 2. Local CSV Ingestion Strategy

This pipeline will perform **locally ingested CSV** processing with the following approach:

- **Most CSV files** will be ingested locally and named with the convention: `oulad_grp3___[table_name]`
  - Examples: `oulad_grp3___student_assessment`, `oulad_grp3___courses`, etc.
- **Student VLE table exception**: The `studentVle.csv` file will NOT be ingested locally since it was already processed and loaded to the remote server by Sir Myk due to its large file size. We will reference this existing table from the `raw` schema.

## Project Structure

```
dlt/
├── extract-loads/
│   ├── oulad-pipeline.py
│   └── staging/
│       └── oulad/
│           ├── assessments.csv
│           ├── courses.csv
│           ├── studentAssessment.csv
│           ├── studentInfo.csv
│           ├── studentRegistration.csv
│           └── studentVle.csv (reference only)
└── pipelines/
    └── oulad-pipeline/
        ├── load/
        ├── normalize/
        ├── schemas/
        ├── state.json
        └── trace.pickle
```

## Implementation Steps

### 1. Setting Up Database Connection

To establish a connection to the database server using DBeaver:

- Open DBeaver and create a new ClickHouse connection
- Configure connection with the following parameters:
    - Host IP: 54.87.106.52 
    - Username: chinook
    - Password: chinook

![img](https://i.imgur.com/CsTo79i.png[/img])

- Create new credentials for the pipeline:
    - Username: ftw_user
    - Password: ftw_pass

### 2. Configuring DLT Pipeline

Create a new file called `oulad-pipeline.py` inside the `extract-loads` folder with the following content:

```python
# dlt/pipeline.py
# dlt/pipeline.py
import dlt, pandas as pd
import os

@dlt.resource(name="assessments", write_disposition="replace")
def assessments():
    ROOT_DIR = os.path.dirname(__file__)
    FILE_PATH = os.path.join(ROOT_DIR, "staging", "oulad", "assessments.csv")
    yield pd.read_csv(FILE_PATH)

@dlt.resource(name="courses", write_disposition="replace")
def courses():
    ROOT_DIR = os.path.dirname(__file__)
    FILE_PATH = os.path.join(ROOT_DIR, "staging", "oulad", "courses.csv")
    yield pd.read_csv(FILE_PATH)

@dlt.resource(name="student_assessment", write_disposition="replace")
def student_assessment():
    ROOT_DIR = os.path.dirname(__file__)
    FILE_PATH = os.path.join(ROOT_DIR, "staging", "oulad", "studentAssessment.csv")
    yield pd.read_csv(FILE_PATH)

@dlt.resource(name="student_info", write_disposition="replace")
def student_info():
    ROOT_DIR = os.path.dirname(__file__)
    FILE_PATH = os.path.join(ROOT_DIR, "staging", "oulad", "studentInfo.csv")
    yield pd.read_csv(FILE_PATH)

@dlt.resource(name="student_registration", write_disposition="replace")
def student_registration():
    ROOT_DIR = os.path.dirname(__file__)
    FILE_PATH = os.path.join(ROOT_DIR, "staging", "oulad", "studentRegistration.csv")
    yield pd.read_csv(FILE_PATH)

@dlt.resource(name="vle", write_disposition="replace")
def vle():
    ROOT_DIR = os.path.dirname(__file__)
    FILE_PATH = os.path.join(ROOT_DIR, "staging", "oulad", "vle.csv")
    yield pd.read_csv(FILE_PATH)

# ----------------------------
# Run pipeline
# ----------------------------

def run_pipeline():
    """Load each OULAD CSV as a separate table"""
    p = dlt.pipeline(
        pipeline_name="oulad-pipeline",
        destination="clickhouse",
        dataset_name="oulad_mk",
    )
    print("Fetching and loading each file as separate resource...")

    info = p.run([
        assessments(),
        courses(),
        student_assessment(),
        student_info(),
        student_registration(),
        vle()
    ])

    print("Records loaded:", info)

if __name__ == "__main__":
    run_pipeline()

```

**Pipeline Approaches Explained:**

- **Approach 1**: Combines all CSV files into a single table with a `_source_file` column
- **Approach 2**: Creates separate tables for each CSV file (recommended approach)
- **Approach 3**: Dynamically discovers all CSV files and combines them into one table

The pipeline is configured to use **Approach 2** by default, which creates separate tables for each CSV file.
    
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

Execute the DLT job using Docker:

```
docker compose --profile jobs run --rm dlt python extract-loads/oulad-pipeline.py
```

For the OULAD data pipeline, configure the run definition:

- Update the run definition to use the group dataset name:
    
    ```python
    def run():
        pipeline = dlt.pipeline(
            pipeline_name="oulad-pipeline",
            destination="clickhouse",
            dataset_name="oulad_grp3",
            dev_mode=False   
        )
    ```
    
**Note about StudentVle**: The pipeline includes a `vle()` function for the VLE (Virtual Learning Environment) data. However, since the studentVle.csv file is large and was pre-loaded to the server, you may choose to exclude it from local processing by commenting out the `vle()` function call in the `run_approach_2()` function.

**Table Naming Convention**: When using Approach 2, DLT will automatically create tables with names like:
- `oulad_grp3___assessments`
- `oulad_grp3___courses`
- `oulad_grp3___student_assessment`
- `oulad_grp3___student_info`
- `oulad_grp3___student_registration`
- `oulad_grp3___vle` (if included)

### 4. Running DBT

After setting up the DLT pipeline, run the DBT transformations:

```
docker compose --profile jobs run --rm -w /workdir/transforms/02_OULAD dbt build --profiles-dir . --target remote
```

- Ensure the naming conventions are correct for the dbt clean folder and dbt mart
- Verify all target tables follow the naming structure: `database.tablename_grp3`

### 5. Creating Analytics Tables

Create analytics tables in the sandbox schema by executing SQL in DBeaver:

1. Right-click on the connection "Remote_FTW" in DBeaver
2. Select SQL Editor > New SQL script
3. Execute SQL to create dimension and fact tables using the OULAD data structure

Example for creating a student dimension table:

```sql
DROP TABLE IF EXISTS sandbox.DimStudent_grp3;
CREATE TABLE sandbox.DimStudent_grp3
ENGINE = MergeTree
ORDER BY tuple()
AS
SELECT
    id_student AS student_key,
    code_module,
    code_presentation,
    gender,
    region,
    highest_education,
    age_band
FROM raw.oulad_grp3___student_info;
```

### 6. Accessing Data Visualization

Data visualization is available through Metabase at:

http://54.87.106.52:3001/browse/databases


## Summary

This project demonstrates the successful implementation of a complete data pipeline that extracts OULAD data from locally stored CSV files, transforms it using DLT and DBT, and loads it into ClickHouse for analytics. The pipeline follows best practices for data engineering, ensuring data is properly organized, efficiently stored, and trustworthy for downstream analysis.

## Notes

- Ensure all CSV files (except studentVle.csv) are downloaded and placed in the `extract-loads/staging/oulad/` directory
- The studentVle table is already available in the raw schema and should be referenced directly
- Replace `grp3` with your actual group identifier throughout the implementation
- Ensure proper naming conventions are maintained across all pipeline components: `oulad_grp3___[table_name]`
- Verify database connections and credentials before running the pipeline
- Monitor the pipeline execution for any errors or data quality issues
