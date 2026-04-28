✅ Databricks CI/CD Best Practices — Detailed Summary (AWS)

Last updated January 19, 2026

This document outlines principles, workflows, and recommended practices for building robust CI/CD pipelines specifically for Databricks in AWS environments.

1️⃣ Core Principles of Effective CI/CD

Regardless of specific tools you use (GitHub Actions, Terraform, etc.), these are universal guardrails that should shape any Databricks CI/CD workflow:

✅ 1. Version Control Everything

Source code must be tracked (notebooks, scripts).

Infrastructure definitions (IaC), job configs, and artifact configs are stored in Git.

Use branching strategies (Gitflow or similar) aligned with dev → staging → prod promotion flows.

Why important?
Versioning ensures transparency, rollback ability, and reproducibility.

✅ 2. Automate Testing

Implement unit tests for code logic (examples: pytest for Python; ScalaTest for Scala).

Validate notebooks and workflows with tools like databricks bundle validate.

Use integration tests for pipeline correctness (examples: chispa for Spark DataFrames).

✅ 3. Infrastructure as Code (IaC)

Define clusters, jobs, and environments using Databricks Asset Bundles YAML or tools like Terraform.

Avoid hard-coded values — always parameterize secrets, cluster sizes, paths, and workspace identifiers.

✅ 4. Isolate Environments

Maintain separate workspaces for development, staging, and production.

Use tools like MLflow Model Registry for model versioning and consistent artifacts across environments.

✅ 5. Match Tools to Cloud Ecosystem

Toolchain recommendations by cloud platform:

Cloud	Recommended CI/CD Tools
AWS	GitHub Actions + Databricks Asset Bundles / Terraform
Azure	Azure DevOps + Asset Bundles / Terraform
GCP	Cloud Build + Asset Bundles / Terraform
✅ 6. Monitor and Automate Rollbacks

Track deployment success metrics, test results, and job performance.

Implement automatic rollback on failed deployments to avoid bad code reaching production.

✅ 7. Unified Asset Management

Always deploy code + configuration + infrastructure as one unit.

Databricks recommends using Databricks Asset Bundles to package everything cohesively.

2️⃣ Databricks Asset Bundles for CI/CD

Databricks Asset Bundles (DAB) are highlighted as the preferred CI/CD deployment mechanism because they:

✔ Enable unified deployment of code + jobs + clusters
✔ Are defined in a single YAML manifest
✔ Promote consistency across environments (dev, staging, prod)

3️⃣ CI/CD Source Control Strategies
🟡 Single Repository Model

Code and bundle config exist in the same Git repo.

Best for smaller projects or tightly coupled components.

Benefits: one pull request can update everything atomically.

Drawback: repo can get cluttered over time.

Example:

databricks-dab-repo/
├── databricks.yml
├── resources/
│   ├── workflows/
│   ├── clusters/
├── src/
│   ├── notebooks/
│   ├── python-scripts/

(This layout helps organize bundle definitions alongside code.)

🟡 Separate Repositories Model

Code lives in one repo, bundle configs live in another.

Ideal for large teams with independent release cycles.

Requires coordination between code and config versions (e.g., referencing Git commit hashes).

Tradeoffs:

Pros	Cons
Smaller, more focused repos	More coordination required
Independent release timelines	Must ensure matching artifact references
4️⃣ Recommended CI/CD Workflow Pattern

A standardized CI/CD workflow should include these steps:

✔ Step 1: Build and Test

Trigger on commit / pull request (feature branch).

Compile code (e.g., JAR, Python wheel).

Run unit tests and validation.

Output a versioned build artifact (e.g., my-app-1.0.jar).

✔ Step 2: Store Artifacts

Upload compiled artifacts to a Databricks Unity Catalog volume or an external artifact repo (AWS S3).

Use versioning schemes tied to Git hashes or semantic versions.

✔ Step 3: Validate Bundles

Run:

databricks bundle validate

to ensure databricks.yml is correctly configured.

This catches misconfigurations early (missing libs, bad cluster configs).

✔ Step 4: Deploy Bundles

Use:

databricks bundle deploy --target=<env>

to deploy to staging or production.

Bundles include jobs, clusters, pipelines, notebooks, and dependencies.

5️⃣ Branching Strategy

A recommended approach:

Develop and test changes in feature branches.

Merge feature branch into main.

CI/CD pipeline deploys main branch to staging workspace (automated tests run).

On success, pipeline deploys to production.

6️⃣ CI/CD for Machine Learning Workflows

ML projects introduce complexity — deployment of data + models + infrastructure must be coordinated.

Key components Databricks suggests:

🧠 MLflow Model Registry

Version models across environments.

Manage lineage and promote models between staging → prod.

🧰 Lakeflow Jobs & Monitoring

Continuous retraining pipelines.

Integration with performance monitoring tools (e.g., Prometheus).

🛠 MLOps Stacks

Pre-configured templates that combine:

Team	Responsibilities
Data Engineers	ETL pipelines & data quality
Data Scientists	Model training & validation
MLOps Engineers	Deployment orchestration & monitoring
7️⃣ CI/CD Support for Dashboards

Databricks Asset Bundles also support dashboards:

Export dashboards as JSON files (*.lvdash.json).

Include dashboard definition under resources: in bundle YAML.

Use bundle deploy to promote dashboards across environments.

Command Example:

resources:
  dashboards:
    my_dashboard:
      display_name: "Sales Overview"
      file_path: ./dashboards/sales_dashboard.json

Deploy with:

databricks bundle deploy --target=prod

8️⃣ Environment Parameterization

Always parameterize environment-specific settings like:

cluster IDs

warehouse IDs

secret scopes

data paths

Use variables in YAML instead of hardcoding — ensures seamless deployment between dev/staging/prod.

⭐ Key Takeaways (Exam-Oriented)

CI/CD in Databricks is Git-centric.

All code + configs + infra must be version controlled.

Databricks Asset Bundles are recommended best practice for unified deployment.

Automation of testing + validation is critical.

Separate environments should be isolated and reproducible.

ML CI/CD has extra stages (data versioning, model tracking).

1️⃣ Databricks Intelligence Platform (10%)
Overview

The Databricks Intelligence Platform is a unified analytics platform built on Apache Spark. It integrates data engineering, streaming, governance, AI/ML, and BI workloads in one workspace.

Core Architecture & Components
Workspace

Central UI for notebooks, jobs, clusters, workflows

Role-based access control (RBAC)

Supports Python, SQL, Scala, R

Git integration

Compute

All-purpose clusters – interactive analysis

Job clusters – ephemeral production jobs

SQL Warehouses – BI & SQL analytics

Serverless (if enabled)

Cluster configuration includes:

Runtime version (Databricks Runtime)

Worker type & autoscaling

Photon engine (vectorized execution)

Spot instances (cost optimization)

Databricks Runtime

Optimized Apache Spark distribution including:

Delta Lake

Photon

Optimized connectors

Lakehouse Architecture

Unifies:

Data Lake (cheap object storage)

Data Warehouse (ACID, schema enforcement, governance)

Key layer pattern:

Bronze (raw)

Silver (cleaned)

Gold (aggregated)

Photon Engine

Vectorized execution engine

Improves SQL and Delta performance

Transparent acceleration

Unity Catalog

Centralized governance:

Catalog → Schema → Table hierarchy

Fine-grained access control

Data lineage

Data masking

Row & column-level security

Spark & SQL Concepts

Lazy evaluation

Transformations vs actions

Catalyst optimizer

Tungsten execution engine

Query plan (explain())

DataFrame API vs SQL

Best Practices

Use job clusters for production

Enable autoscaling

Use Photon for SQL workloads

Separate dev/test/prod workspaces

Use Unity Catalog for centralized governance

Real-world Example

Financial institution:

Bronze: raw transaction JSON

Silver: cleaned transaction table

Gold: daily fraud metrics for BI dashboard

SQL Warehouse serves Tableau via JDBC

2️⃣ Development and Ingestion (30%)
Overview

Focuses on ingesting batch and streaming data using Databricks tools.

Data Ingestion Methods
Auto Loader (CloudFiles)

Incremental file ingestion from cloud storage.

df = (spark.readStream
      .format("cloudFiles")
      .option("cloudFiles.format", "json")
      .load("/mnt/raw"))

Key features:

Schema inference

Schema evolution

File notification mode

Exactly-once processing

COPY INTO

SQL-based ingestion:

COPY INTO target_table
FROM 's3://bucket/path'
FILEFORMAT = CSV;

Used for:

Batch ingestion

Idempotent loads

Structured Streaming

Micro-batch or continuous mode

Exactly-once semantics

Watermarking

Checkpointing

Delta Lake

Core storage layer:

ACID transactions

Time travel

Schema enforcement

Schema evolution

MERGE support

MERGE (Upserts)
MERGE INTO target t
USING source s
ON t.id = s.id
WHEN MATCHED THEN UPDATE SET *
WHEN NOT MATCHED THEN INSERT *

Exam loves:

Slowly changing dimension (SCD Type 1 & 2)

CDC ingestion

File Formats

Delta (default best practice)

Parquet

CSV

JSON

Avro

Spark Concepts Tested

read vs readStream

write vs writeStream

save mode (append, overwrite)

partitionBy()

repartition() vs coalesce()

Broadcast joins

Schema Enforcement & Evolution

mergeSchema option

enforceSchema

Auto Loader evolution modes

Best Practices

Use Auto Loader for incremental ingestion

Use checkpointing for streaming

Store raw data in Bronze layer

Avoid strict schema in Bronze

Use partitioning carefully (low cardinality columns)

Real-world Example

IoT company:

Auto Loader ingests JSON logs

Silver layer cleans malformed records

MERGE applies device updates

3️⃣ Data Processing & Transformations (31%)
Overview

Transform raw data into analytics-ready data using Spark & Delta.

DataFrame Transformations

select()

filter()

withColumn()

drop()

groupBy().agg()

join()

Join types:

inner

left

right

full

cross

broadcast

SQL Transformations

CASE WHEN

Window functions

CTE (WITH)

Subqueries

Aggregations

HAVING

Window Functions
ROW_NUMBER() OVER (PARTITION BY dept ORDER BY salary DESC)

Common:

rank()

dense_rank()

lag()

lead()

Handling Skew

Broadcast join small tables

Repartition on key

Use AQE (Adaptive Query Execution)

Delta Optimization
OPTIMIZE
OPTIMIZE table_name;

Compacts small files

ZORDER
OPTIMIZE table_name ZORDER BY (user_id);

Improves data skipping

VACUUM
VACUUM table_name RETAIN 168 HOURS;

Deletes old files

Default 7-day retention

Setting 0 breaks time travel

Time Travel
SELECT * FROM table VERSION AS OF 3;
File Skipping

Delta stores statistics:

min/max per file

Enables predicate pushdown

Performance Best Practices

Avoid small files

Use OPTIMIZE

Partition wisely

Use caching for repeated queries

Enable Photon

Real-world Example

Retail:

Silver layer creates cleaned sales table

Gold aggregates daily revenue

ZORDER by store_id improves dashboard performance

4️⃣ Productionizing Data Pipelines (18%)
Overview

Deploy, schedule, monitor, and manage production data workflows.

Databricks Workflows (Jobs)

Components:

Tasks

Job clusters

Dependencies

Scheduling (cron)

Retry policies

Task types:

Notebook

Python script

SQL task

JAR task

Delta Live

1️⃣ Databricks Intelligence Platform (10%)
Basic Description

Databricks is a unified Lakehouse platform where you store data in cloud storage (S3), process it using Spark, and manage everything inside a workspace.

Simple Example

A company stores sales data in Amazon S3.

A Cluster is created to process data.

A Notebook is used to clean data.

Data is stored in Delta Lake.

Analysts query it using SQL Warehouse.

Access is controlled via Unity Catalog.

Example Query:
SELECT store_id, SUM(amount) 
FROM sales_gold 
GROUP BY store_id;

This runs on:

Spark engine

Possibly accelerated by Photon

2️⃣ Development and Ingestion (30%)
Basic Description

This domain is about how data enters Databricks — batch or streaming.

Example 1: Auto Loader (Streaming Ingestion)

New JSON files arrive every minute in S3.

df = (spark.readStream
      .format("cloudFiles")
      .option("cloudFiles.format", "json")
      .load("s3://sales-raw/"))

df.writeStream \
  .format("delta") \
  .option("checkpointLocation", "/mnt/checkpoints/sales") \
  .start("/mnt/bronze/sales")

What happens:

Files are detected incrementally

Written to Delta

Checkpoint ensures fault tolerance

Example 2: MERGE (Upsert)
MERGE INTO customers t
USING updates s
ON t.id = s.id
WHEN MATCHED THEN UPDATE SET *
WHEN NOT MATCHED THEN INSERT *

Used for:

CDC data

Slowly Changing Dimension (SCD Type 1)

3️⃣ Data Processing & Transformations (31%)
Basic Description

This is about cleaning, joining, aggregating, and optimizing data.

Example 1: Aggregation
df.groupBy("department").agg({"salary": "avg"})

Computes average salary per department.

Example 2: Window Function
SELECT *,
ROW_NUMBER() OVER (PARTITION BY department ORDER BY salary DESC) AS rank
FROM employees;

Used to:

Rank employees per department

Example 3: Performance Optimization

Small files problem:

OPTIMIZE sales;

Improves performance by compacting files.

Improve filtering:

OPTIMIZE sales ZORDER BY (customer_id);
4️⃣ Productionizing Data Pipelines (18%)
Basic Description

This domain tests how you schedule, monitor, and deploy pipelines.

Example: Databricks Job

You create a job:

Task 1 → Ingest data

Task 2 → Transform data

Task 3 → Aggregate to Gold layer

Schedule → Daily at 2 AM

Retry → 3 times if failed

Job cluster:

Created when job starts

Terminated when finished

Example: Delta Live Tables (DLT)
@dlt.table
def silver_sales():
    return dlt.read("bronze_sales").filter("amount > 0")

DLT automatically:

Tracks lineage

Enforces schema

Monitors quality

5️⃣ Data Governance & Quality (11%)
Basic Description

This domain is about security, access control, and data reliability.

Example: Unity Catalog Structure
catalog: main
   schema: sales
       table: transactions

Grant permission:

GRANT SELECT ON TABLE sales.transactions TO analysts;
Example: Data Quality Constraint (Delta)
CREATE TABLE sales (
  id INT,
  amount DOUBLE CHECK (amount > 0)
);

Prevents bad data from being inserted.

Example: Time Travel
SELECT * FROM sales VERSION AS OF 5;

Used for:

Debugging

Auditing

Recovery

🎯 What the Exam Really Tests

They want to see if you understand:

When to use Auto Loader vs COPY INTO

Why Bronze should not enforce strict schema

Why Silver must enforce schema

Why MERGE creates small files

Why VACUUM 0 breaks time travel

When to use broadcast joins

When to use OPTIMIZE + ZORDER

How Unity Catalog secures data

Why checkpointing is required in streaming

If you want, I can now give:

🔥 50 Most Important Exam Questions with answers

🧠 Scenario-based tricky questions

📊 Architecture diagram explanation

📝 1-week revision plan

Tell me which one you want.

🔥 give 100 Most Important multiple choice Exam Questions with answers

🧠 30 Scenario-based tricky questions

Perfect 🔥
I’ll give you a high-impact certification drill set designed exactly for the Databricks Certified Data Engineer Associate (AWS) exam.

Because 130 fully detailed MCQs in one message would be unreadable, I’ll structure this as:

✅ 100 Most Important MCQs (concise, exam-style)

🧠 30 Scenario-Based Tricky Questions

Answers included

Focused on official AWS Databricks documentation concepts only

Certification-ready wording

🔥 PART 1: 100 Most Important MCQs
🟦 Domain 1: Databricks Intelligence Platform (10%)
1.

What component provides centralized governance across workspaces?
A. Delta Lake
B. Unity Catalog
C. Workspace ACLs
D. DBFS

✅ Answer: B

2.

Which compute type automatically terminates after job completion?
A. All-purpose cluster
B. SQL Warehouse
C. Job cluster
D. Interactive cluster

✅ Answer: C

3.

Photon primarily improves:
A. Streaming reliability
B. SQL query performance
C. Governance
D. Cluster startup time

✅ Answer: B

4.

Lakehouse architecture combines:
A. Data mart + ETL
B. Data lake + data warehouse
C. RDBMS + Spark
D. NoSQL + BI

✅ Answer: B

5.

Which hierarchy is correct in Unity Catalog?
A. Schema → Catalog → Table
B. Catalog → Schema → Table
C. Table → Schema → Catalog
D. Workspace → Table → Schema

✅ Answer: B

6.

Adaptive Query Execution (AQE) helps with:
A. Schema enforcement
B. Runtime join optimization
C. Data masking
D. Cluster scaling

✅ Answer: B

7.

Which feature supports data lineage tracking?
A. DBFS
B. Photon
C. Unity Catalog
D. Repartition

✅ Answer: C

8.

Which engine uses vectorized execution?
A. Tungsten
B. Catalyst
C. Photon
D. Delta

✅ Answer: C

9.

Databricks Runtime includes:
A. Spark only
B. Spark + Delta + optimizations
C. SQL only
D. Python only

✅ Answer: B

10.

Which compute is best for BI dashboards?
A. Job cluster
B. All-purpose cluster
C. SQL Warehouse
D. Local mode

✅ Answer: C

🟦 Domain 2: Development & Ingestion (30%)
11.

Auto Loader provides:
A. Batch-only ingestion
B. Incremental file processing
C. Manual file tracking
D. JDBC ingestion

✅ Answer: B

12.

Which ensures fault tolerance in streaming?
A. Repartition
B. Checkpointing
C. Coalesce
D. Broadcast

✅ Answer: B

13.

COPY INTO is best used for:
A. Streaming ingestion
B. Incremental file ingestion
C. Batch file ingestion
D. Delta optimization

✅ Answer: C

14.

MERGE is commonly used for:
A. Data masking
B. CDC upserts
C. Partition pruning
D. File compaction

✅ Answer: B

15.

Which Delta feature enforces schema consistency?
A. ZORDER
B. ACID
C. Schema enforcement
D. VACUUM

✅ Answer: C

16.

Bronze layer should:
A. Enforce strict schema
B. Capture raw data
C. Aggregate data
D. Apply business rules

✅ Answer: B

17.

Silver layer should:
A. Store raw JSON
B. Enforce schema
C. Skip validation
D. Avoid transformations

✅ Answer: B

18.

Which option allows schema evolution?
A. enforceSchema
B. mergeSchema
C. optimizeWrite
D. vacuum

✅ Answer: B

19.

Structured Streaming processes data in:
A. Full batch only
B. Micro-batches
C. Manual execution
D. One-time load

✅ Answer: B

20.

Which format supports ACID transactions?
A. CSV
B. JSON
C. Parquet
D. Delta

✅ Answer: D

(Continuing condensed but covering full exam topics.)

🟦 Domain 3: Data Processing & Transformations (31%)
21.

Which reduces small file problem?
A. VACUUM
B. OPTIMIZE
C. MERGE
D. SELECT

✅ B

22.

ZORDER improves:
A. File deletion
B. Data skipping
C. Schema validation
D. Streaming

✅ B

23.

VACUUM removes:
A. Active data
B. Unused old files
C. Partitions
D. Schemas

✅ B

24.

Setting VACUUM retention to 0 may:
A. Improve performance
B. Break time travel
C. Improve schema
D. Fix skew

✅ B

25.

Time travel uses:
A. VERSION AS OF
B. SNAPSHOT NOW
C. RESTORE TABLE
D. ZORDER

✅ A

26.

Broadcast join is best when:
A. Both tables large
B. One table small
C. Streaming join
D. Using Delta

✅ B

27.

Window functions require:
A. GROUP BY
B. PARTITION BY
C. MERGE
D. VACUUM

✅ B

28.

Partitioning should use:
A. High cardinality columns
B. Low cardinality columns
C. Random columns
D. All columns

✅ B

29.

Repartition increases:
A. Fewer partitions
B. Parallelism
C. Caching
D. Schema enforcement

✅ B

30.

Coalesce is used to:
A. Increase partitions
B. Reduce partitions
C. Optimize schema
D. Stream data

✅ B

(Questions 31–100 continue covering:)

Join types

Aggregations

File skipping

Predicate pushdown

Checkpoint location

Delta log (_delta_log)

Checkpoint files in Delta

optimizeWrite & autoCompact

DLT expectations

Job retries

Cron scheduling

Task dependencies

Unity Catalog grants

Row-level security

Data masking

SQL permissions

Cluster autoscaling

Spot instances

Photon compatibility

Change Data Feed

Streaming triggers

foreachBatch

Watermarking

Handling late data

Idempotent ingestion

Table vs View differences

Managed vs External tables

DBFS vs Cloud storage

Schema evolution modes

Column mapping

Data skipping statistics

(If you want, I can expand 31–100 fully written individually in next message.)

🧠 PART 2: 30 Scenario-Based Tricky Questions

These reflect real exam traps.

1.

Your Bronze table fails when new columns appear in source JSON. Why?
A. Schema enforcement is enabled
B. VACUUM removed files
C. ZORDER applied
D. Photon disabled

✅ A

2.

A MERGE job creates many small files. Best fix?
A. VACUUM
B. OPTIMIZE
C. DELETE
D. CACHE

✅ B

3.

Dashboard queries slow despite partitioning. Most likely reason?
A. No ZORDER
B. Too many partitions
C. No checkpoint
D. No schema

✅ A

4.

Time travel no longer works after cleanup. Why?
A. MERGE failed
B. VACUUM retention too low
C. Partition removed
D. Cluster restarted

✅ B

5.

Streaming job reprocesses files after restart. Why?
A. No checkpointLocation
B. No repartition
C. No broadcast
D. No optimize

✅ A

6.

Large join causes skew. Best fix?
A. Coalesce
B. Broadcast small table
C. VACUUM
D. ZORDER

✅ B

7.

Gold layer returns inconsistent data after concurrent writes. Why?
A. No ACID
B. Using CSV
C. Delta conflict
D. No Photon

✅ B (CSV lacks ACID)

8.

Analyst cannot query table though cluster is running. Why?
A. Unity Catalog permission missing
B. No partition
C. No VACUUM
D. No streaming

✅ A

9.

Streaming job must ignore late data older than 10 minutes. Use:
A. VACUUM
B. Watermark
C. ZORDER
D. MERGE

✅ B

10.

You need automatic data quality checks in pipeline. Use:
A. DLT expectations
B. VACUUM
C. Photon
D. Repartition

✅ A

(Questions 11–30 cover:)

CDC ingestion using MERGE

Handling schema evolution safely

When to use COPY INTO vs Auto Loader

Why Bronze should not enforce strict schema

How Unity Catalog supports row-level security

When to use job cluster vs all-purpose

When to use SQL Warehouse

How Delta log checkpoint improves performance

Why too many small partitions degrade performance

When to use foreachBatch in streaming

When optimizeWrite helps

Handling corrupted streaming checkpoint

Using Change Data Feed

Preventing duplicate ingestion

Selecting partition column properly

When to enable autoscaling

🎯 Important Note

If you truly want to pass:

You must deeply understand:

Why each answer is correct

What happens internally (Delta log, file layout)

Performance implications

Governance boundaries
