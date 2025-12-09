# Medallion Architecture Project: GitHub CSV -> Sales Dashboard

## Overview

This document is a full technical design and implementation plan for a medallion-architecture pipeline that pulls a CSV from GitHub and produces a production-ready sales star schema exposed to a dashboard. It contains folder structure, sample SQL/dbt models, Airflow/Databricks job specs, CI/CD notes, monitoring, and an initial runbook.

Target environment assumptions

* Storage: ADLS Gen2 (Delta Lake) or equivalent object store that supports Delta.
* Compute and orchestration: Databricks notebooks/Jobs and Airflow / Azure Data Factory for orchestration.
* Transformations: dbt (v1.x recommended).
* Dashboard: Tableau (Desktop / Cloud) connecting to Databricks SQL Warehouse or live cluster.

ASSUMPTION: the dataset's business key is `enquiryNumber`. Confirm this before wide rollout.

---

# Table of contents

1. Project summary and goals
2. Folder and repo layout
3. Bronze / Silver / Gold design
4. Schema design and star schema DDL
5. Sample dbt project structure and sample models
6. Extractor (GitHub) implementation - Python example
7. Orchestration: Airflow / Databricks Jobs specs
8. CI / CD and testing strategy
9. Data quality and monitoring
10. Security and governance
11. Tableau and dashboard publishing notes
12. Runbook and operational playbook
13. Appendix: sample queries and utilities

---

# 1. Project summary and goals

## Goals

* Ingest CSV files from GitHub automatically and store immutable raw snapshots.
* Clean and conformed Silver layer for downstream processing.
* Build a normalized star schema (Gold) for sales analytics, with dimensions and fact table(s).
* Provide lineage and traceability to source file SHA and pull timestamp.
* Enable incremental refreshes and minimal recompute.

## Deliverables

* Extractor script (Python) to pull from GitHub and write to Bronze.
* dbt project with staging models, conformed models, dims, and fact.
* Orchestration DAG and Databricks Job specs.
* Documentation, field mappings, and tests.
* Tableau workbook skeleton and connection instructions.

---

# 2. Folder and repo layout

This repo layout is designed for clarity and CI/CD. Two repos are recommended: one for infra/pipelines and one for analytics (dbt + dashboards).

```
/infra-pipelines/              # extractor, Airflow DAGs, IaC
  /extractor/                  # python extractor code
    main.py
    requirements.txt
    Dockerfile
  /airflow/dags/
    dag_github_to_bronze.py
  /scripts/
    deploy.sh
  /terraform/                  # optional infra IaC

/dbt-analytics/                # dbt project and artifacts
  /models/
    /staging/
      stg_github_sales.sql
    /marts/
      /core/
        dim_date.sql
        dim_contact.sql
        dim_salesrep.sql
        dim_location.sql
        fact_enquiry.sql
  dbt_project.yml
  profiles.yml
  packages.yml
  README.md
  /tests/

/tableau/
  sales_dashboard.twbx
  publish_script.sh

/docs/
  data_dictionary.md
  runbook.md
```

Notes

* Keep extractor and pipeline infra separate from dbt code so data engineers and analytics engineers can iterate independently.
* Use environment-specific configs: `dev`, `qa`, `prod` in `profiles.yml` and Airflow connections.

---

# 3. Bronze / Silver / Gold design

## Bronze (raw)

* Goal: immutable snapshot of the original CSV plus file-level metadata.
* Storage path pattern (Delta or Parquet):

  * `/lake/bronze/github_sales/source=github_owner_repo/path=relative_path/ingest_date=YYYY-MM-DD/file_sha=SHA/`
* Stored artifacts:

  * Original CSV file (optionally compressed).
  * `manifest` JSON with `file_sha`, `commit_id`, `pull_ts`, `raw_file_path`, `file_size`, `row_count`.

## Silver (cleaned / conformed)

* Goal: typed columns, normalized categorical values, deduplicated rows, basic enrichment.
* Core tasks:

  * parse timestamps to ISO 8601, standardize dates, normalize `mobile_no` to E.164 style, canonicalize state/district names, map `enq_stage` values to canonical codes.
  * split out columns where necessary and drop obviously duplicated or junk columns (but keep raw copy if necessary for audits).
  * write per-table Delta tables e.g. `silver.stg_github_sales` and `silver.enquiries_conformed`.

## Gold (analytics and star schema)

* Goal: normalized dimensions + fact tables, ready for BI consumption.
* Materialize `dim_*` and `fact_enquiry` tables. Use surrogate keys and optional SCD Type 2 for contacts and salesreps.

---

# 4. Schema design and star schema DDL

## Assumed canonical field mapping

* `enquiryNumber` -> business key
* `enq_stage` -> lead stage
* `expected_date_of_purchase` -> expected_purchase_date
* `enqcreatedon` -> enq_created_ts
* `mobile_no` -> contact_phone
* `Sales_name` -> salesrep_name
* `TMname` -> team_manager_name
* `district`, `state`, `tehsil`, `village` -> location fields
* `DCdate`, `DCnumber` -> delivery/confirmation fields
* `Isenq_Validated` -> is_validated (Y/N)
* `next_follow_up_date` -> next_followup_date
* `3` -> unknown column; inspect raw and map after verification

### Gold DDL (example SQL)

```sql
-- dim_date (surrogate key is date_key int YYYYMMDD)
CREATE TABLE gold.dim_date (
  date_key INT PRIMARY KEY,
  date DATE,
  year INT,
  quarter INT,
  month INT,
  day INT,
  day_of_week INT,
  is_weekend BOOLEAN
) USING DELTA;

-- dim_contact
CREATE TABLE gold.dim_contact (
  contact_id BIGINT GENERATED ALWAYS AS IDENTITY,
  mobile_e164 STRING,
  first_seen_date_key INT,
  hashed_mobile STRING,
  current_enquiry_number STRING,
  is_current BOOLEAN,
  effective_from_ts TIMESTAMP,
  effective_to_ts TIMESTAMP
) USING DELTA;

-- dim_salesrep
CREATE TABLE gold.dim_salesrep (
  salesrep_id BIGINT GENERATED ALWAYS AS IDENTITY,
  salesrep_name STRING,
  tm_name STRING,
  territory STRING,
  is_current BOOLEAN,
  effective_from_ts TIMESTAMP,
  effective_to_ts TIMESTAMP
) USING DELTA;

-- dim_location
CREATE TABLE gold.dim_location (
  location_id BIGINT GENERATED ALWAYS AS IDENTITY,
  state STRING,
  district STRING,
  tehsil STRING,
  village STRING,
  location_hash STRING
) USING DELTA;

-- fact_enquiry
CREATE TABLE gold.fact_enquiry (
  enquiry_id BIGINT GENERATED ALWAYS AS IDENTITY,
  enquiryNumber STRING,
  contact_id BIGINT,
  salesrep_id BIGINT,
  location_id BIGINT,
  source_id INT,
  enq_stage_id INT,
  enq_created_date_key INT,
  expected_purchase_date_key INT,
  next_followup_date_key INT,
  is_validated BOOLEAN,
  dc_number STRING,
  dc_date TIMESTAMP,
  remarks STRING,
  record_created_ts TIMESTAMP,
  record_updated_ts TIMESTAMP
) USING DELTA;
```

Notes

* Use `MERGE` semantics when loading incremental data into fact and dims to avoid duplicates.
* Use surrogate keys for joins in BI tools.

---

# 5. Sample dbt project structure and sample models

## dbt_project.yml (high level)

```yaml
name: sales_medallion
version: '1.0'
config-version: 2
profile: sales_medallion_profile
source-paths: ["models"]
target-path: "target"
analysis-paths: ["analysis"]
```

## models/staging/stg_github_sales.sql (example)

```sql
-- models/staging/stg_github_sales.sql
with raw as (
  select
    file_sha,
    TRIM(enquiryNumber) as enquiry_number,
    TRIM(enq_stage) as enq_stage_raw,
    TRY_TO_TIMESTAMP(enqcreatedon) as enq_created_ts,
    TRY_TO_DATE(expected_date_of_purchase, 'M/d/yyyy') as expected_purchase_date,
    regexp_replace(mobile_no, '[^0-9]', '') as mobile_digits,
    CASE WHEN length(regexp_replace(mobile_no, '[^0-9]', '')) = 10
      THEN concat('91', regexp_replace(mobile_no, '[^0-9]', ''))
      WHEN length(regexp_replace(mobile_no, '[^0-9]', '')) = 12 THEN regexp_replace(mobile_no, '[^0-9]', '')
      ELSE NULL END as mobile_e164,
    Sales_name as sales_name,
    district,
    state,
    tehsil,
    village,
    DCdate,
    DCnumber,
    Isenq_Validated
  from {{ source('bronze', 'github_sales') }}
)

select *
from raw
```

## models/marts/core/dim_date.sql (example)

```sql
-- generate date dimension for a range
with dates as (
  select
    to_date(date_add('1970-01-01', s.i)) as date
  from generate_series(0, 3650) as s(i)
)
select
  cast(date_format(date, '%Y%m%d') as int) as date_key,
  date,
  year(date) as year,
  quarter(date) as quarter,
  month(date) as month,
  day(date) as day,
  dayofweek(date) as day_of_week,
  (case when dayofweek(date) in (1,7) then true else false end) as is_weekend
from dates
```

## models/marts/core/fact_enquiry.sql (example)

```sql
with conformed as (
  select
    enquiry_number,
    mobile_e164,
    sales_name,
    state,
    district,
    enq_created_ts,
    expected_purchase_date,
    next_follow_up_date,
    is_validated,
    dc_number,
    dc_date,
    row_number() over (partition by enquiry_number order by enq_created_ts desc nulls last) as rn
  from {{ ref('stg_github_sales') }}
)

select
  enquiry_number,
  mobile_e164,
  sales_name,
  state,
  district,
  cast(date_format(enq_created_ts, '%Y%m%d') as int) as enq_created_date_key,
  cast(date_format(expected_purchase_date, '%Y%m%d') as int) as expected_purchase_date_key,
  cast(date_format(next_follow_up_date, '%Y%m%d') as int) as next_followup_date_key,
  case when is_validated='Y' then true else false end as is_validated,
  dc_number,
  dc_date
from conformed
where rn = 1
```

## tests and schema

* Use `unique` test on `enquiry_number` in staging and final fact.
* Use `not_null` on `enquiry_number`, `enq_created_date_key`.
* Value tests for `mobile_e164` pattern.

---

# 6. Extractor (GitHub) implementation - Python example

This extractor will pull the CSV from GitHub using the contents API and write the file and manifest to ADLS Gen2 via the cloud SDK or to Databricks DBFS.

### Key behavior

* Authenticate with GitHub PAT stored in a secrets manager.
* GET the file content with `GET /repos/{owner}/{repo}/contents/{path}`.
* If file is base64-encoded in the response, decode and persist.
* Compute SHA or use returned `sha` to detect new files.
* Upload CSV to `/bronze/github/.../ingest_date=.../file_sha=...` and write `manifest.json`.

### sample code snippet (simplified)

```python
import requests
import base64
import json
from datetime import datetime

GITHUB_API = 'https://api.github.com/repos/{owner}/{repo}/contents/{path}'
HEADERS = {'Authorization': f'token {GITHUB_TOKEN}'}

resp = requests.get(GITHUB_API.format(owner=OWNER, repo=REPO, path=PATH), headers=HEADERS)
resp.raise_for_status()
j = resp.json()
file_sha = j['sha']
content_b64 = j['content']
content = base64.b64decode(content_b64)

# write content to local file or upload to ADLS / DBFS
with open(f'/tmp/{file_sha}.csv', 'wb') as f:
    f.write(content)

manifest = {
  'file_sha': file_sha,
  'path': PATH,
  'pull_ts': datetime.utcnow().isoformat(),
  'size': len(content)
}

with open(f'/tmp/{file_sha}.manifest.json','w') as f:
    json.dump(manifest, f)
```

Notes

* For private repos, ensure the PAT has `repo` permissions.
* If files are large, use `raw.githubusercontent.com` download or Git LFS approaches.

---

# 7. Orchestration: Airflow / Databricks Jobs specs

## DAG overview

* DAG schedule: configurable (hourly/daily).
* Tasks:

  1. `extract_github` - run extractor container or script.
  2. `bronze_validation` - lightweight job to register file, row count.
  3. `dbt_run_staging` - run dbt models for staging.
  4. `dbt_run_marts` - run dbt models for gold.
  5. `post_checks` - data quality tests and metrics logging.
  6. `notify` - on failure or completion.

### Example Airflow task (pseudo)

```python
with DAG('github_to_medallion', schedule_interval='@daily') as dag:
    t1 = BashOperator(task_id='extract', bash_command='python /opt/extractor/main.py')
    t2 = DatabricksSubmitRunOperator(task_id='bronze_validation', ...)
    t3 = BashOperator(task_id='dbt_staging', bash_command='dbt run --models staging')
    t4 = BashOperator(task_id='dbt_marts', bash_command='dbt run --models marts')
    t5 = PythonOperator(task_id='post_checks', python_callable=post_checks)

    t1 >> t2 >> t3 >> t4 >> t5
```

## Databricks Job spec (example)

* Job 1: `bronze_process` - Cluster config, notebook to register file and write to Bronze Delta.
* Job 2: `run_dbt` - if dbt is run inside Databricks, mount repo and run dbt commands in job.

---

# 8. CI / CD and testing strategy

## CI pipeline (GitHub Actions)

* On PR: run `flake8` / `pytest` for extractor, run `dbt ls` and `dbt compile` and lightweight dbt tests using a small sample dataset in ephemeral dev environment.
* On merge to `main`: run full dbt run against `prod` target via GitHub Actions or Databricks job trigger.

## Testing

* Unit tests for extractor.
* dbt tests: `unique`, `not_null`, `accepted_values` and custom SQL tests for date plausibility.
* End-to-end integration test: run extractor on sample file and validate expected rows in `gold.fact_enquiry`.

---

# 9. Data quality and monitoring

## Key checks

* Row count changes vs. baseline.
* Unique `enquiryNumber` violations.
* Percent invalid phone numbers.
* Null date rates for key fields.

## Monitoring stack

* Push metrics to a `monitoring` table in the lake and surface via a lightweight Grafana / Tableau dashboard.
* Configure alerts for failing dbt tests or Airflow job failures.

---

# 10. Security and governance

* Store GitHub token in Key Vault / Secret Manager.
* Encrypt data at rest.
* Mask or hash `mobile_no` at presentation layer if PII policy requires.
* Role-based access to Gold tables; limit Bronze access to engineers only.

---

# 11. Tableau and dashboard publishing notes

* Connect Tableau Desktop to Databricks SQL Warehouse using the Databricks connector.
* Build extracts for high-cardinality visuals; publish to Tableau Cloud or Server and schedule refreshes.
* Use surrogate keys in Star Schema for joins.

---

# 12. Runbook and operational playbook

## Common failures and remedies

* GitHub auth errors: rotate PAT, confirm permissions.
* Schema drift: capture schema snapshot in manifest and fail pipeline if unexpected new columns appear.
* Duplicate ingestion: check `file_sha` in manifest and skip if already ingested.

## Recovery steps

1. Identify failing job via Airflow UI or Databricks job logs.
2. Re-run the failed task after resolving root cause.
3. If data corrected in source, reprocess by adding an entry to manifest with `reprocess=true`.

---

# 13. Appendix: sample utilities

* `normalize_phone.py` - python utility to standardize phone.
* `map_values.yaml` - mapping file for `enq_stage`, `source_of_enq`, `state` canonicalization.

# 14. Next steps and handshake

* I created the full technical design, folder structure, and starter artifacts in this document.
* If you want, I can now:

  * produce the extractor script with full ADLS upload (next artifact), or
  * scaffold the dbt project with the critical models and tests, or
  * write the Airflow DAG and Databricks Job JSON specs.

Pick which of the three to build next and I will produce runnable code in the next message.
