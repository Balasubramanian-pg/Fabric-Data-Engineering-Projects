# The Project Plan

The overview of the movement of the data is as such :

* Ingest CSV from GitHub via GitHub REST API into a Bronze raw layer.
* Clean, standardize, and validate into a Silver conformed layer.
* Build normalized star schema (dimensions and fact tables) and materialize Gold analytics tables.
* Orchestrate with a scheduler and CI/CD, add testing, monitoring, governance, and expose to Tableau or Power BI for a Sales Dashboard.

Key references used: Databricks Medallion architecture, GitHub REST API docs, dbt dimensional modeling guidance, and Tableau <-> Databricks connectivity docs. ([Databricks][1])

So let us start with the scope and goals of the project
# 1. Project scope and goals

## Goals

* Produce a clean, auditable, production-ready Sales star schema for dashboarding.
* Keep lineage from raw GitHub CSV to final dashboard tiles.
* Support incremental loads and reprocessing.
* Keep PII handling and access controls explicit.

## Deliverables

* Ingestion pipeline that pulls CSV from GitHub and lands into Bronze (Delta/Parquet).
* Data quality and standardization layer producing Silver tables.
* Star schema models (dimension tables and fact_sales) in Gold and dbt models to manage transformations.
* Orchestration jobs, tests, and CI/CD.
* A connected Tableau dashboard with live/refreshable data.
* Documentation, monitoring, and access controls.

# 2. Data understanding and immediate questions (items that need verification)

These points must be clarified before final physical model design:

1. What is the canonical unique business key? Is `enquiryNumber` unique across the dataset?
2. What does the column named `3` contain? It looks like a stray name. Needs inspection.
3. What do `DCdate` and `DCnumber` represent and are they authoritative for order/fiscal data?
4. Is `mobile_no` considered PII that must be masked or tokenized? What privacy policy applies?
5. Expected data volume and refresh cadence from GitHub: daily, hourly, ad-hoc?
6. Private repo vs public repo access. If private, we must use personal access token for GitHub API.

List of uncertain points requiring verification:

* Meaning of `3`.
* Confirm `enquiryNumber` uniqueness.
* Target deployment platform: Databricks / Azure Synapse / Snowflake / plain ADLS Gen2.
* Retention and archival policy for Bronze raw files.

# 3. Architecture overview (medallion layers)

### Bronze *(raw ingestion)*

  * Purpose: immutable raw snapshots of CSVs as landed from GitHub. Keep original file plus metadata (source, pull timestamp, sha). Store as Delta or Parquet with partitioning by ingest_date.
  * Notes: keep one raw file per commit/pull for traceability. Use GitHub REST API `GET /repos/{owner}/{repo}/contents/{path}` or raw download_url for public files with optional token for private repos. ([GitHub Docs][2])

### Silver *(cleaned, conformed)*

  * Purpose: parsed and typed columns, deduplicated, normalized values for fields like `enq_stage`, `state`, `source_of_enq`. Resolve inconsistent encodings, normalize phone numbers, parse timestamps, map categorical values to canonical codes.
  * Techniques: type coercion, null handling, phone regex, standardized date parsing using ISO 8601, mapping table lookups for stages and sources.

### Gold *(analytics / star schema)*

  * Purpose: dimensional model exposed to BI. Build conformed dimensions and fact table(s) for sales/enquiries with grain = one enquiry event or one sales interaction depending on business definition. Materialize performance-optimized tables and aggregates.
  * Use-case tables: `dim_date`, `dim_customer` (or `dim_contact`), `dim_location`, `dim_salesrep`, `dim_source`, and `fact_enquiry` or `fact_sales_activity`.

Medallion architecture background and best practices references. ([Databricks][1])

# 4. Ingestion design (GitHub -> Bronze)

## Steps

1. **Authentication**

   * Use GitHub REST API with a personal access token for private repos. For public repos you can use the raw `download_url`. The repository contents endpoint is recommended. ([GitHub Docs][2])

2. **Pull**

   * Implement a lightweight extractor (Python or Azure Function) that:

     * Calls GitHub API for the file content and metadata (sha, last_modified).
     * Saves the raw CSV and a small metadata JSON containing `source_repo`, `path`, `sha`, `pull_ts`, `commit_id`.

3. **Landing**

   * Write raw file and metadata into ADLS Gen2 path like:

     * `/bronze/github/sales_enquiry/source_repo=xxx/yyy/ingest_dt=YYYY-MM-DD/file=sha.csv`
   * Prefer Delta format if using Databricks to enable ACID and efficient MERGE later.

4. **Schema capture**

   * Create a `bronze_manifest` table that stores file-level metadata and schema inference results for future audits.

## Implementation tips

* Use incremental pull: compare `sha` in manifest to avoid reprocessing identical files.
* Log failures and retries.

# 5. Bronze -> Silver processing (clean, standardize, validate)

## Main tasks

* **Parsing and typing**

  * Parse date strings: `expected_date_of_purchase`, `next_follow_up_date`, `enqcreatedon`, `DCdate`. Convert to UTC-aware timestamps or store with timezone info if available.
* **Column renames**

  * Rename messy columns to canonical names. Example: `3` -> `enq_priority` or inspect to determine true meaning.
* **Value normalization**

  * Map `enq_stage` values into canonical codes: e.g., GENUINE PROSPECT -> `GENUINE`, HOT/OVER DUE/COLD into `lead_status` mapping table.
* **Phone normalization**

  * Normalize `mobile_no` to country code +91 format using regex and drop non-numeric characters.
* **Deduplication**

  * Deduplicate using business key `enquiryNumber` and latest `enqcreatedon` or `DCdate` depending on source reliability.
* **Validation rules**

  * Check required fields presence, mobile number validity, date plausibility (expected purchase not earlier than createdon), and flag exceptions into a `silver_exceptions` table.
* **Enrichment**

  * Geocode or map `district`, `state`, `tehsil`, `village` to standardized location identifiers if available.

## Sample SQL steps (conceptual)

* Parse and cast:

```sql
SELECT
  enquiryNumber,
  TRY_TO_TIMESTAMP(enqcreatedon) AS enq_created_ts,
  TRY_TO_DATE(expected_date_of_purchase, 'M/d/yyyy') AS expected_purchase_date,
  regexp_replace(mobile_no, '[^0-9]', '') AS mobile_digits,
  CASE WHEN mobile_digits LIKE '91%' THEN mobile_digits ELSE concat('91', mobile_digits) END AS mobile_e164,
  TRIM(UPPER(state)) AS state_norm,
  ...
FROM bronze.github_sales
```

* Deduplicate using window:

```sql
SELECT * EXCEPT(rank) FROM (
  SELECT *, ROW_NUMBER() OVER (PARTITION BY enquiryNumber ORDER BY enq_created_ts DESC) AS rank
  FROM silver.staging_enquiries
) WHERE rank = 1
```

# 6. Silver -> Gold: star schema design

## Grain decisions

* Fact grain: one row per `enquiryNumber` event. If you need multiple activities per enquiry, use `fact_enquiry_activity` with activity grain.

## Suggested dimension tables

* `dim_date`

  * columns: `date_key (YYYYMMDD int)`, `date`, `year`, `quarter`, `month`, `day`, `is_weekend`, `fiscal_month`, etc.
* `dim_contact` or `dim_customer`

  * columns: `contact_id (surrogate)`, `mobile_e164`, `first_seen_date_key`, `latest_enquiry_number`, hashed PII if needed.
* `dim_salesrep`

  * columns: `salesrep_id`, `sales_name`, `tm_name`, `territory`, `hire_date` (if available).
* `dim_location`

  * columns: `location_id`, `state`, `district`, `tehsil`, `village`, `geo_code` (if enriched).
* `dim_source`

  * columns: `source_id`, `source_of_enq`, `subsource`, `channel_type`.
* `dim_enq_stage`

  * mapping of `enq_stage` codes to business-friendly labels.

## Fact table: `fact_enquiry`

* columns:

  * `enquiry_id` surrogate PK
  * `enquiryNumber` business key
  * `contact_id` FK
  * `salesrep_id` FK
  * `location_id` FK
  * `source_id` FK
  * `enq_stage_id` FK
  * `expected_purchase_date_key` FK to `dim_date`
  * `enq_created_date_key` FK to `dim_date`
  * `next_follow_up_date_key` FK to `dim_date`
  * numeric metrics and flags: `is_validated` (Y/N), `dc_number`, `dc_date`, `remarks`, `lead_score` (if derived)
  * `record_created_ts`, `record_updated_ts`

## SCD handling

* Dimensions with slowly changing attributes: implement SCD Type 2 for `dim_contact` and `dim_salesrep` if you need historical attribution. Use `effective_from`, `effective_to`, and `is_current` flags.

Design reference for dimensional modeling and dbt workflows. ([dbt Labs][3])

# 7. Implementation technologies and why

* **Storage and compute**

  * Delta Lake on ADLS Gen2 or Databricks: ACID, efficient MERGE for incremental pipeline. Delta + Medallion pattern is common. ([Delta][4])

* **Transformation**

  * **dbt** for modular SQL models, testing, documentation, and lineage. Use dbt for Silver->Gold transformations and dimension logic. ([dbt Labs][3])

* **Orchestration**

  * Airflow, Azure Data Factory, or Databricks Jobs for scheduling and dependency management. Use job runs that call the extractor, bronze write, run dbt models, and notify.

* **CI/CD**

  * Use GitHub Actions to run dbt tests, linting, unit tests on pull requests, and deploy to production branches.

* **Observability**

  * Implement data quality tests (dbt tests), row-count assertions, and anomaly detection. Store pipeline metrics in a monitoring table and push alerts to Slack/email.

* **BI**

  * Tableau or Tableau Cloud connecting directly to Databricks SQL Warehouse or to the Gold tables. Databricks provides a Tableau connector for low-latency access. ([Databricks Documentation][5])

# 8. End-to-end pipeline orchestration (detailed flow)

1. **Schedule extractor**

   * Cron: hourly/daily depending on cadence. Extractor calls GitHub REST API, downloads CSV, writes to Bronze path and registers manifest row.

2. **Bronze validation job**

   * Quick schema parse, copy to `bronze.staging_raw`, basic file-level checks, and register metadata.

3. **Silver transform job**

   * Run dbt staging models to:

     * Clean types, normalize values, apply mapping tables, deduplicate, and write `silver` tables.
   * Run dbt tests for constraints and anomalies.

4. **Gold transform job**

   * Run dbt models to build dims and facts. Materialize incremental tables with `merge` logic for facts and `insert/update` patterns for dimensions.

5. **Post-run**

   * Trigger data quality checks, update metrics dashboard, and refresh Tableau extracts or notify live connection.

6. **Monitoring and alerting**

   * Pipeline success/failure, data quality failures, new schema changes detected in Bronze.

# 9. Example dbt model structure and naming

* `models/`

  * `stg/github_sales/`  <-- staging models mapping raw columns to canonical fields

    * `stg_github_sales.sql`
    * `schema.yml` (tests)
  * `int/`  <-- intermediate conformed tables

    * `int_enquiries.sql`
  * `dim/`

    * `dim_contact.sql`
    * `dim_salesrep.sql`
    * `dim_location.sql`
    * `dim_date.sql`
  * `fact/`

    * `fact_enquiry.sql`

Use dbt tests: `unique`, `not_null`, `relationships`. Use `sources:` definitions to point at Bronze tables.

# 10. Data quality, testing, and lineage

## Tests

* Uniqueness on `enquiryNumber` in staging and fact where appropriate.
* Not null checks on key foreign keys.
* Value range checks for dates and phone numbers.
* Duplicate detection and audit logs for rows collapsed by dedupe.

## Lineage

* dbt docs will surface lineage from staging to final gold. Persist commit hashes in manifest to tie final rows back to source file SHA.

# 11. Security, governance, and compliance

* Mask or hash `mobile_no` if required. Use column-level access to restrict PII exposure.
* Secure GitHub token in Key Vault or Secrets Manager.
* Audit logs for who ran jobs and when.
* Data retention: keep Bronze raw for X months, purge policy based on retention requirement.

# 12. Tableau dashboarding and UX notes

## Connection

* Connect Tableau to Databricks SQL Warehouse using the Databricks connector for live queries or scheduled extracts for performance. ([Databricks Documentation][5])

## Suggested dashboard pages

* **Executive summary:** KPIs: total enquiries, validated leads, hot vs cold, expected purchases by month.
* **Funnel view:** enq_stage progression, conversion rates.
* **Geography:** map by `state/district/tehsil`.
* **Sales rep performance:** leads handled, conversion, average time to follow up.
* **Data quality panel:** percent missing phone, date parsing failures, rows flagged.

## Performance tips

* Pre-aggregate heavy measures in Gold if live queries are slow. Use materialized aggregate tables for week/month. Use Tableau extracts during peak hours.

# 13. CI/CD and deployment

* **Repo layout**

  * `/infrastructure` IaC scripts (Terraform for ADLS, Databricks workspace).
  * `/pipelines` extractor code.
  * `/dbt` models and docs.
  * `/dashboards` Tableau workbook files or publishing scripts.

* **Pipelines**

  * PR triggers: run unit dbt tests, lints, and a lightweight integration run against a test environment.
  * Merge to main triggers deployment to prod environment via GitHub Actions.

# 14. Observability, cost control, and runbook

* Track daily row counts in Bronze/Silver/Gold and run-time for each job.
* Budget controls: limit cluster runtime, use spot instances when permissible.
* Create runbook for common failures: GitHub auth errors, schema drift, credential rotation.

# 15. Minimal first sprint (MVP) plan — what I would do first

1. Confirm assumptions and get answers to verification list.
2. Implement a small extractor that pulls the CSV from GitHub and lands into `/bronze/github/...` along with manifest. (1-2 days).
3. Create a staging dbt model `stg_github_sales` that parses and normalizes a few critical fields, plus 5 dbt tests. (2-3 days).
4. Implement `dim_date` and a simple `fact_enquiry` materialized table with dedupe logic. (2 days).
5. Connect a Tableau workbook to the Gold table and produce a 3-view dashboard: KPI summary, funnel, and table. (1-2 days).

This MVP will give a working end-to-end path and let us iterate on schema and additional dimensions.

# 16. Deliverables I will provide if you want me to proceed next

* Full technical design document with folder structure, sample SQL/dbt models, and Airflow/Databricks job specs.
* Example extractor script (Python) that calls GitHub API, handles private token, and writes to ADLS Gen2 or Databricks.
* dbt project skeleton with `stg_github_sales`, `dim_date`, `fact_enquiry` and sample tests.
* Example Tableau workbook skeleton or instructions to connect.

# 17. Concise reasoning steps I took to create this plan

* Read dataset sample and identified noisy columns and PII risks.
* Applied the medallion pattern because it maps naturally to incremental cleaning and lineage for raw CSVs. ([Databricks][1])
* Selected dbt for transformations because it is suited to building and testing modular SQL models for dimensional design. ([dbt Labs][3])
* Chose Delta/ADLS Gen2 and Databricks-style connectivity to support ACID writes and Tableau connectivity for dashboards. ([Delta][4])

# 18. Next concrete actions

* I will produce the extractor script that downloads the CSV from GitHub and writes it to Bronze plus a sample manifest entry.
* I will scaffold the dbt project with `stg_github_sales` including parsing and the first tests.
* I will design the star schema DDL and give exact SQL for creating `dim_date`, `dim_contact`, and `fact_enquiry`.

[1]: https://www.databricks.com/glossary/medallion-architecture?utm_source=chatgpt.com "What is a Medallion Architecture?"
[2]: https://docs.github.com/rest/repos/contents?utm_source=chatgpt.com "REST API endpoints for repository contents"
[3]: https://www.getdbt.com/blog/guide-to-dimensional-modeling?utm_source=chatgpt.com "A complete guide to dimensional modeling with dbt"
[4]: https://delta.io/blog/delta-lake-medallion-architecture/?utm_source=chatgpt.com "Building the Medallion Architecture with Delta Lake"
[5]: https://docs.databricks.com/aws/en/partners/bi/tableau?utm_source=chatgpt.com "Connect Tableau and Databricks"
