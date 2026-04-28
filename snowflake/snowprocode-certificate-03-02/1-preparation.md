<summary>Micro‑partitions & Data Clustering — Summary</summary>
<details>
  
## 📘 Overview
Snowflake uses **micro‑partitioning** and **clustering** to optimize storage, pruning, and query performance without the limitations of traditional static partitioning.

## 🧩 What Are Micro‑partitions?
- All Snowflake tables are automatically divided into **micro‑partitions** (50–500 MB uncompressed).
- Stored in **columnar** format and **compressed**.
- Snowflake maintains metadata for each micro‑partition:
  - Column value ranges  
  - Distinct counts  
  - Additional optimization stats  

### ⭐ Benefits
- No manual partitioning required.
- Very fine‑grained pruning → faster queries.
- Overlapping partitions reduce data skew.
- Efficient columnar scanning (only needed columns are read).
- Metadata‑only operations for some DML (e.g., delete all rows).

## 🔍 Query Pruning
Snowflake uses micro‑partition metadata to skip unnecessary data.

Example:  
A table with 1 year of hourly data → a query for 1 hour ideally scans **1/8760** of micro‑partitions.

Pruning works best when:
- Data is well‑clustered  
- Filters use simple predicates (not subqueries)

## 📊 What Is Data Clustering?
Clustering refers to how well data is ordered along certain columns (e.g., date, region).

Snowflake:
- Tracks clustering metadata for each micro‑partition.
- Uses it to prune partitions more efficiently.
- Allows optional **clustering keys** for large or frequently‑queried tables.

## 📏 Clustering Depth
A measure of how much micro‑partitions overlap for selected columns.

- Depth **1** = well‑clustered  
- Higher depth = more overlap → less efficient pruning  
- Empty table = depth **0**

Used for:
- Monitoring table clustering health  
- Deciding whether clustering keys are needed  

Note: Query performance is the real indicator of clustering quality.

## 🛠️ Monitoring Tools
Snowflake provides:
- `SYSTEM$CLUSTERING_DEPTH`
- `SYSTEM$CLUSTERING_INFORMATION`

These functions help evaluate clustering quality and micro‑partition overlap.

## 🎯 In Short
Snowflake’s micro‑partitioning:
- Automates partitioning with no user maintenance.
- Enables extremely efficient pruning and fast queries.
- Works with clustering metadata to optimize large tables.
- Supports optional clustering keys when performance declines.

</details>

<summary>
Snowflake Storage Considerations</summary>
<details>Snowflake storage costs are influenced by how data moves through Continuous Data Protection (CDP)—Active, Time Travel, and Fail-safe. Understanding table types, cloning behavior, and churn patterns helps control storage usage and cost.
# Snowflake Storage Considerations — Concise Summary

## 📌 Overview
Snowflake storage costs depend on how data moves through **Continuous Data Protection (CDP)**: Active storage, Time Travel, and Fail-safe. Understanding table types, cloning behavior, and data churn helps optimize cost and performance.

# 1. Continuous Data Protection (CDP)

Snowflake protects data through three sequential states:

| State | Description | Cost Impact |
|-------|-------------|-------------|
| **Active** | Current table data | Charged |
| **Time Travel** | Historical versions (0–90 days) | Charged |
| **Fail-safe** | 7-day recovery after Time Travel | Charged |

### Key Notes
- Updates/deletes create new micro-partitions; old ones remain in CDP → **extra storage cost**.
- Even small but frequent updates can accumulate large storage usage.

# 2. Monitoring Storage

### Account-Level Monitoring
- Snowsight → **Admin → Cost Management → Consumption**

### Table-Level Monitoring
- Snowsight → **Catalog → Database Explorer**
- `SHOW TABLES`
- `TABLE_STORAGE_METRICS` (most detailed):
  - `ACTIVE_BYTES`
  - `TIME_TRAVEL_BYTES`
  - `FAILSAFE_BYTES`
  - Ownership vs. referenced bytes (important for clones)

# 3. Staged File Storage

- Internal stages incur **standard storage cost** (no CDP).
- Best practice: **remove staged files** after loading.
- Purging improves load performance.

# 4. Cloning & Storage

Snowflake uses **zero-copy cloning**:

- Clones initially share micro-partitions → **no extra storage**.
- Any changes create new micro-partitions owned by the modified object.
- Clone groups can be multi-level.
- When a table is dropped and Time Travel expires, storage ownership shifts to remaining clones.

# 5. Table Types & Cost Management

Snowflake offers table types to control CDP cost:

### Permanent Tables
- Full CDP (Active + Time Travel + Fail-safe)
- Best for long-lived, critical data

### Temporary Tables
- Session-scoped
- No Fail-safe; Time Travel = 0–1 day
- Max CDP cost = 1 day

### Transient Tables
- Persist beyond session
- No Fail-safe; Time Travel = 0–1 day
- Ideal for staging or short-lived data

# 6. Managing High-Churn Tables

High-churn = frequent updates/deletes → many micro-partition rewrites → high CDP cost.

### Identify High Churn
Use the ratio:

### Best Practices
- Most dimension tables → keep **permanent**.
- For extremely high-churn large tables:
  - Use **transient tables** with **0-day Time Travel**
  - Periodically copy into a permanent backup table
  - Delete old backups to control cost

This can reduce storage from **tens of TB → a few TB** in extreme cases.

# 7. Key Takeaways

- CDP is powerful but can increase storage cost for frequently updated tables.
- Use `TABLE_STORAGE_METRICS` to monitor Active vs. Time Travel vs. Fail-safe bytes.
- Use temporary/transient tables for short-lived or high-churn workloads.
- Purge staged files regularly.
- Understand clone behavior to avoid unexpected storage growth.

</details>

<summary>Snowflake Virtual Warehouses — Concise Summary</summary>

<details>
## 📌 What is a Warehouse?
A **virtual warehouse** provides the compute resources required for:
- Query execution  
- DML operations  
- Data loading  

Warehouses can be **started, stopped, and resized at any time**.

# 1. Warehouse Size
Warehouse size determines compute capacity per cluster.

Sizes (Gen1):  
**X-Small → Small → Medium → Large → X-Large → 2X-Large → 3X-Large → 4X-Large → 5X-Large → 6X-Large**

### Key Points
- Each size roughly **doubles compute and credit usage**.
- Snowflake bills **per second** (60‑second minimum on start).
- Larger warehouses = faster for complex queries, but **not always faster** for small queries or data loading.

# 2. Billing & Credit Usage
- Credits scale with warehouse size and runtime.
- Multi‑cluster warehouses bill per cluster.
- Larger warehouses do **not** always improve load performance; file count matters more.

# 3. Multi‑Cluster Warehouses
- Automatically add/remove clusters to handle concurrency.
- Enterprise Edition feature.
- Useful when many users or workloads run simultaneously.

# 4. Auto‑Suspend & Auto‑Resume
- **Auto‑suspend** stops the warehouse after inactivity.
- **Auto‑resume** starts it when a query arrives.
- Prevents unnecessary credit consumption.

# 5. Query Processing & Concurrency
- Each query reserves compute resources.
- If insufficient resources → query is **queued**.
- Options to manage queuing:
  - Increase warehouse size
  - Use multi‑cluster warehouses
  - Create additional warehouses

# 6. Warehouse Defaults in Sessions
A session needs a warehouse before running queries.

Default warehouse can be set:
- For a **user**
- In **client tools** (SnowSQL, JDBC, Python connector, etc.)
- Via **connection parameters**
- Changed anytime using `USE WAREHOUSE`

# 7. Notebook Warehouse (Preview)
- Snowflake auto‑provisions a **multi‑cluster X-Small warehouse** for notebooks:
  - Name: `SYSTEM$STREAMLIT_NOTEBOOK_WH`
  - Up to 10 clusters
  - 60‑second timeout
- Recommended for **notebook Python workloads only** to reduce cost and fragmentation.

# 8. Best Practices
- Use **auto‑suspend** to avoid idle costs.
- Use **smaller warehouses** for most data loads.
- Use **larger warehouses** only for complex queries.
- Use **multi‑cluster** for concurrency, not performance.
- Separate notebook workloads from SQL workloads.

</details>

<summary>Snowflake Multi‑Cluster Warehouses</summary>
<details># Snowflake Multi‑Cluster Warehouses — Concise Summary

## 📌 Overview
A **multi‑cluster warehouse** is a Snowflake virtual warehouse that can automatically start and stop additional clusters to handle varying query concurrency. It improves **throughput and concurrency**, not raw query speed.

# 1. Why Multi‑Cluster?
- Prevents **query queuing** during high user or workload concurrency.
- Automatically scales compute **horizontally** by adding clusters.
- Ideal for:
  - BI dashboards
  - Large numbers of simultaneous users
  - Mixed workloads (ETL + reporting)

# 2. Min/Max Clusters
A multi‑cluster warehouse defines:
- **Minimum clusters** (always running)
- **Maximum clusters** (upper limit for scaling)

Snowflake automatically adjusts cluster count based on load.

# 3. Scaling Policies

### **Standard Policy**
- Adds clusters only when queries are queued.
- Removes clusters when load decreases.
- Best for cost efficiency.

### **Economy Policy**
- More conservative scaling.
- Adds clusters more slowly.
- Best for minimizing credit usage.

# 4. Auto‑Suspend & Auto‑Resume
- Each cluster can auto‑suspend when idle.
- Warehouse auto‑resumes when new queries arrive.
- Helps reduce credit consumption.


# 5. Billing Behavior
- You are billed **per running cluster**.
- If 3 clusters are active, cost = 3× warehouse size rate.
- Clusters shut down independently when not needed.

# 6. Key Use Cases
- High‑concurrency BI workloads
- Many short queries running simultaneously
- Shared warehouses across teams
- Avoiding query queuing without oversizing a single warehouse


# 7. Best Practices
- Use **Standard** scaling for most workloads.
- Use **Economy** when cost control is more important than responsiveness.
- Set **min = 1** and **max > 1** for dynamic scaling.
- Use separate warehouses for ETL and BI when workloads conflict.

</details>

<summary>Snowflake Object Cloning</summary>
<details>Snowflake supports **zero‑copy cloning**, allowing instant, cost‑efficient duplication of databases, schemas, and tables. Clones initially share the same micro‑partitions as the source, and only diverge when data changes.

---

# 1. What Zero‑Copy Cloning Means
- Cloning creates a **logical copy** of an object.
- No data is physically duplicated at creation.
- Both source and clone reference the **same micro‑partitions**.
- Cloning is **instant**, regardless of object size.

---

# 2. Storage Behavior (Including CN Documentation Details)

### Shared Storage
- Source and clone share all micro‑partitions until one object is modified.
- Shared partitions are billed **only once**.

### Divergence on Change
- Any DML/DDL that modifies data creates **new micro‑partitions**.
- Only changed partitions consume additional storage.
- Unchanged partitions remain shared.

### Time Travel & Fail-safe Effects
- Cloning preserves Time Travel history at the moment of cloning.
- Dropping the source object does **not** remove shared data until:
  - All clones referencing those partitions expire from Time Travel  
  - Fail-safe retention completes

### Storage Accounting (from CN docs)
- Storage metrics show:
  - **Active bytes** owned by the clone
  - **Referenced bytes** shared with the source
- Clones can significantly reduce storage cost compared to full copies.

---

# 3. Supported & Unsupported Objects

### Supported for Cloning
- Databases  
- Schemas  
- Tables  
- Streams  
- Stages, file formats, sequences, tasks (with limitations)

### Not Supported
- External tables  
- Views (logical objects only copy metadata, not data)

---

# 4. Metadata Behavior
- Clones copy:
  - Table structure  
  - Constraints  
  - Metadata properties  
  - Time Travel retention settings  
- Clones **do not inherit privileges** — grants must be re‑applied.

---

# 5. Time Travel Cloning
You can clone an object **as of a specific point in time**:
CREATE TABLE t_clone CLONE t AT (TIMESTAMP => '2024-01-01 00:00:00');
Uses:
- Restoring corrupted data  
- Reproducing historical states  
- Creating test environments from past snapshots  

---

# 6. Clone Chains & Lifecycle
- Clones can be created from clones (multi‑level).
- Storage ownership shifts as objects are dropped.
- Shared data persists until **all referencing clones** age out of CDP.

---

# 7. Common Use Cases
- **Dev/Test environments** without duplicating data.
- **Backup & recovery** using Time Travel.
- **ETL testing** without impacting production.
- **Experimentation** with schema or data changes.

---

# 8. Best Practices
- Use cloning instead of full copies to save storage.
- Drop old clones to release storage tied to modified partitions.
- Re‑grant privileges after cloning.
- Use Time Travel cloning for safe rollback workflows.
</details>

<summary>Snowflake Time Travel, Fail-safe & CDP</summary>
<details># Snowflake Time Travel, Fail-safe & CDP
## Overview
Snowflake provides **Continuous Data Protection (CDP)** to safeguard historical data. CDP includes:
- **Time Travel** → User-accessible historical data (query, clone, restore)
- **Fail-safe** → Snowflake-managed disaster recovery
- **CDP Storage Costs** → Storage consumed by historical micro-partitions

These features ensure recoverability, auditability, and protection against accidental or malicious data loss.

# 1. Time Travel

## 🔹 What Time Travel Provides
Time Travel allows you to:
- Query historical data  
- Restore dropped tables, schemas, and databases  
- Clone objects **as of a past point in time**  

## 🔹 Retention Periods
| Edition | Default | Maximum |
|--------|----------|----------|
| Standard | 1 day | 1 day |
| Enterprise & above | 1 day | Up to 90 days |

Retention applies to:
- Tables (permanent, transient, temporary)
- Schemas
- Databases

## 🔹 How It Works
- Snowflake stores **historical micro-partitions** for the retention period.
- Updates/deletes create **new partitions**, while old ones remain for Time Travel.
- Dropping an object does not immediately remove its data; it remains recoverable until retention expires.

## 🔹 Time Travel Operations
Examples:

```sql
SELECT * FROM my_table AT (TIMESTAMP => '2024-01-01 00:00:00');
UNDROP TABLE my_table;
CREATE TABLE t_clone CLONE t BEFORE (STATEMENT => '...');

```

# Snowflake Fail-safe, CDP Storage Costs, Table Type Behavior & Cloning Impact  
## 2. Fail-safe

### 🔹 Purpose
Fail-safe is a **Snowflake-managed** 7‑day recovery period that begins **after Time Travel expires**.

Fail-safe is designed for:
- Disaster recovery  
- Catastrophic failures  
- Internal Snowflake recovery operations  

### 🔹 Key Characteristics
- **Not user-accessible**  
- **Not intended for routine recovery**  
- **Snowflake must perform the restore**  
- Data is stored in a **secure, immutable state**  

### 🔹 Retention Timeline Example  
For a permanent table with **1-day Time Travel**:
Day 0–1: Time Travel (user-accessible)
Day 2–8: Fail-safe (Snowflake-accessible only)
## 3. CDP Storage Costs

### 🔹 What Consumes CDP Storage?
Storage is charged for:
- **Active data**
- **Time Travel historical partitions**
- **Fail-safe protected partitions**

### 🔹 Why Storage Grows
- Updates/deletes rewrite micro-partitions → old ones retained for CDP  
- Dropping objects retains data until **Time Travel + Fail-safe** expire  
- High-churn tables generate large amounts of historical data  

### 🔹 Monitoring Storage
Use:
- **Snowsight → Admin → Cost Management → Storage**
- `TABLE_STORAGE_METRICS` view:
  - `ACTIVE_BYTES`
  - `TIME_TRAVEL_BYTES`
  - `FAILSAFE_BYTES`
  - `RETAINED_FOR_CLONE` (shared partitions)

## 4. Interaction with Table Types

| Table Type   | Time Travel | Fail-safe | Notes |
|--------------|-------------|-----------|-------|
| **Permanent** | 1–90 days | 7 days | Full CDP protection |
| **Transient** | 0–1 day | ❌ None | Lower-cost, no Fail-safe |
| **Temporary** | 0–1 day | ❌ None | Session-scoped |

**Transient & temporary tables reduce long-term CDP storage cost.**

## 5. CDP Behavior with Cloning

### 🔹 Zero-Copy Cloning
- Clones share micro-partitions with the source.  
- **No extra storage** until data changes.

### 🔹 CDP Impact
- Shared partitions remain until **all clones** age out of Time Travel.  
- Dropping the source does **not** remove shared data if clones still reference it.

## 6. Best Practices for Managing CDP Costs

### Reduce Unnecessary CDP Storage
- Use **transient** or **temporary** tables for staging/high-churn workloads.  
- Set **shorter Time Travel retention** for large tables.  
- Avoid row-by-row updates; use **bulk operations** instead.  
- Periodically **rebuild high-churn tables** to reset CDP footprint.  

### Monitor & Optimize
- Track `TIME_TRAVEL_BYTES` and `FAILSAFE_BYTES`.  
- Identify high-churn tables using:
FAILSAFE_BYTES / ACTIVE_BYTES
- Drop unused clones to release shared storage.

## 7. Key Takeaways
- **Time Travel** = user-accessible historical data (1–90 days).  
- **Fail-safe** = Snowflake-managed recovery (7 days).  
- **CDP storage** grows with updates, deletes, and dropped objects.  
- **Transient/temporary tables** reduce CDP cost.  
- **Cloning** is storage-efficient but extends retention of shared partitions.  
- Monitoring CDP metrics is essential for cost control.
</details>

<summary>Snowflake Data Sharing</summary>
<details>## 📌 Overview
Snowflake Data Sharing enables organizations to **share live, real-time data** securely with other Snowflake accounts **without copying, moving, or transferring data**. Consumers query shared data instantly, and providers maintain full control.

# 1. Key Concepts

## 🔹 Zero-Copy Sharing
- No data is copied or transferred.
- Consumers access data directly through Snowflake’s metadata layer.
- Shared data **does not consume consumer storage**.
- Consumers pay only for compute used to query the shared data.

## 🔹 Read-Only Access
- All shared objects are **read-only** for consumers.
- Consumers cannot modify or delete shared data.

# 2. What Can Be Shared?
Snowflake allows sharing of many object types, including:

### **Data Objects**
- Databases  
- Tables  
- Dynamic tables  
- External tables  
- Apache Iceberg tables (managed & external)  
- Delta Lake tables (with Delta Direct or catalog-linked databases)

### **Views**
- Regular views  
- Secure views  
- Secure materialized views  
- Semantic views  

### **Functions & Models**
- Secure & non-secure UDFs  
- Models: `USER_MODEL`, `CORTEX_FINETUNED`, `DOC_AI`

### **Services**
- Cortex Search Services  

# 3. How Secure Data Sharing Works

## 🔹 Provider Side
1. Provider creates a **share**.
2. Provider adds objects (tables, views, etc.) to the share.
3. Provider grants access to a consumer account or organization.

## 🔹 Consumer Side
1. Consumer **imports** the share as a database.
2. Consumer can query shared data immediately.
3. Consumer uses their own compute warehouse to run queries.

# 4. Types of Data Sharing

## 🔹 Direct Sharing (Account-to-Account)
- Provider shares directly with another Snowflake account.
- Fastest and simplest method.

## 🔹 Cross-Region / Cross-Cloud Sharing
- Snowflake replicates metadata across regions/clouds.
- Enables global data distribution.

## 🔹 Data Exchange
- Private or public exchanges for curated data sharing.
- Providers can publish datasets to multiple consumers.

## 🔹 Snowflake Marketplace
- Public marketplace for commercial data products.
- Providers can monetize datasets.

# 5. Billing Model

### Provider
- Pays for storage of the shared data.
- Pays for replication if sharing across regions/clouds.

### Consumer
- Pays **only for compute** used to query shared data.
- No storage cost for shared objects.

# 6. Security & Governance

### Built-in Protections
- Data never leaves Snowflake.
- No file transfers or exports required.
- All shared objects are read-only.

### Access Control
- Providers can revoke access at any time.
- Changes to shared data are reflected instantly for consumers.
- Providers maintain full ownership and governance.

# 7. Benefits of Snowflake Data Sharing

- **Real-time access** to live data  
- **Zero data copies** → lower storage cost  
- **Instant provisioning** for consumers  
- **Strong security** with read-only access  
- **Cross-cloud and cross-region** sharing  
- **Ideal for collaboration**, data monetization, and partner ecosystems  

# 8. Common Use Cases

- Sharing data with internal teams or subsidiaries  
- Partner data collaboration  
- Vendor/customer data distribution  
- Data monetization via Snowflake Marketplace  
- Eliminating ETL pipelines for data exchange  

# 9. Key Takeaways

- Snowflake Data Sharing is **secure, instant, and zero-copy**.  
- Consumers pay only for compute; providers pay for storage.  
- Supports a wide range of object types including tables, views, UDFs, and models.  
- Enables cross-region, cross-cloud, and marketplace-based sharing.  
- All shared data is **read-only** and governed centrally by the provider.</details>

<summary>Snowflake Semi‑Structured Data</summary>
<details>## 📌 Overview
Snowflake natively supports **semi‑structured data** such as JSON, Avro, Parquet, ORC, and XML.  
It allows storing, querying, and analyzing these formats **without predefined schemas**, using the flexible `VARIANT`, `OBJECT`, and `ARRAY` data types.

---

# 1. What Is Semi‑Structured Data?

Semi‑structured data:
- Does **not** follow a fixed relational schema.
- Contains **tags, keys, or nested structures**.
- Can evolve over time (new attributes appear anytime).
- Examples: **JSON, Avro, Parquet, ORC, XML**.

Key differences:
- **Structured data** → fixed schema required before loading.  
- **Semi‑structured data** → schema‑on‑read; structure interpreted at query time.

# 2. How Snowflake Stores Semi‑Structured Data

### 🔹 VARIANT Data Type
- Primary type for storing semi‑structured data.
- Can hold **nested objects, arrays, numbers, strings, booleans**.
- Automatically compressed and optimized.

### 🔹 Internal Representation
- Snowflake stores semi‑structured data in a **columnar format**.
- Automatically extracts metadata for efficient pruning.
- Supports deep nesting and complex structures.

# 3. Loading Semi‑Structured Data

### Supported File Formats
- JSON  
- Avro  
- Parquet  
- ORC  
- XML  

### Loading Methods
- `COPY INTO <table>` from internal/external stages.
- Snowpipe for continuous ingestion.
- Automatic parsing into `VARIANT`.

Example:

```sql
CREATE TABLE events (data VARIANT);

COPY INTO events
FROM @mystage
FILE_FORMAT = (TYPE = JSON);

```

4. Querying Semi‑Structured Data
🔹 Dot Notation & Bracket Notation
sql
SELECT data:userId, data:event.type FROM events;
🔹 Path Extraction
Use colon (:) for object keys.

Use brackets ([ ]) for array elements.

🔹 Type Casting
sql
SELECT data:age::INTEGER FROM users;
5. Working with Arrays & Nested Structures
🔹 FLATTEN Function
Used to expand arrays into rows.

Example:
sql
SELECT f.value
FROM events,
LATERAL FLATTEN(input => data:items) f;
🔹 LATERAL Joins
Allow joining nested elements with parent rows.

6. Performance & Optimization
🔹 Automatic Optimization
Snowflake automatically:

Compresses semi‑structured data.
Prunes micro‑partitions using metadata.
Optimizes storage and query performance.
🔹 Best Practices
Use VARIANT for raw ingestion.
Normalize into relational tables only when needed.
Use FLATTEN sparingly; pre‑flatten for heavy workloads.
Use OBJECT_KEYS, ARRAY_SIZE, etc., for efficient navigation.

7. Converting Semi‑Structured to Structured Data
Methods
SELECT … :field::TYPE to extract fields.

Use FLATTEN + SELECT to create relational tables.
Use COPY INTO <table> with SELECT to transform during load.
Example:

sql
CREATE TABLE clean AS
SELECT
  data:id::STRING AS id,
  data:details.name::STRING AS name
FROM raw;
8. Benefits of Snowflake for Semi‑Structured Data
Schema‑on‑read flexibility.
No need for ETL preprocessing before loading.
Native SQL querying of nested structures.
Automatic optimization (compression, pruning).
Unified storage for structured + semi‑structured data.
High performance even with deeply nested JSON.
9. Key Takeaways
Snowflake treats semi‑structured data as a first‑class citizen using VARIANT.
You can load JSON/Avro/Parquet/XML without defining a schema.
Query using SQL with dot notation, path expressions, and FLATTEN.
Snowflake automatically optimizes storage and performance.
Ideal for logs, events, API responses, IoT data, and evolving schemas.
Code

</details>

