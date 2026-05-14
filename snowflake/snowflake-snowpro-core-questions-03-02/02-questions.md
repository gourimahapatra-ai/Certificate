### Question
In which layer of Snowflake architecture are metadata statistics stored?

### Answer
Cloud Services Layer

### Summary
- Metadata statistics are stored in the **Cloud Services Layer**
- Used for **query optimization and micro-partition pruning**

---

### Question
Can data in Fail-safe be deleted by a user or the Snowflake team before it expires?

### Answer
No


### Summary
- Data in **Fail-safe cannot be deleted early**
- It is **system-managed by Snowflake**
- Retained for **7 days after Time Travel expires**
- Not accessible to users; only for disaster recovery by Snowflake

---

### Question
Was Snowflake built from the ground up for the cloud instead of using an existing database or platform like Hadoop?

### Answer
✅ True

### Summary
- Snowflake is a **cloud-native data warehouse**
- Built **from scratch specifically for the cloud**
- Not based on **existing databases or platforms like Hadoop**
- Snowflake's data warehouse was built from the ground up specifically for the cloud, rather than being based on an existing database or platform like Hadoop. This design allows Snowflake to fully leverage the scalability, elasticity, and performance benefits of cloud infrastructure, offering unique features such as:

- **Automatic scaling**
- **Data sharing**
- **Multi-cluster architecture**

---

### Question
What does the PUT command do? (Choose two.)

A. Automatically creates a File Format object  
B. Automatically uses the last Stage created  
C. Automatically compresses files using Gzip  
D. Automatically encrypts files  

### Answer
C. Automatically compresses files using Gzip  
D. Automatically encrypts files  


### Summary
- Files are **automatically compressed (Gzip)** during upload  
- Files are **automatically encrypted** when staged  
- Does **not create File Format objects**  
- Does **not use last created stage implicitly**

---

### Question  
When a Pipe is recreated using the CREATE OR REPLACE PIPE command:

### Answer  
- The existing Pipe is **dropped and recreated**
- The **load history is lost**
- Any **unloaded data may be reprocessed (duplicate loads possible)**

### Summary
- Pipe recreation **resets metadata (load history)**
- Can lead to **duplicate data loads**
- Use cautiously in production

---

### Question  
A data analyst notices that a query on a large table is running significantly slower than expected. They open the query profile and observe that a large amount of data has been spilled to remote storage. Which action would most directly address this performance issue?

### Answer  
Increase the size of the virtual warehouse

### Summary
- Data spilling to remote storage indicates **insufficient memory**
- Larger warehouse = **more memory + compute**
- Reduces or eliminates **disk spilling**, improving performance

---

### Question  
The COPY INTO command in Snowflake allows the use of WHERE clauses to filter data during the loading process.

### Answer  
False

### Summary
- `COPY INTO` **does not support WHERE clauses**
- Data filtering must be done using:
  - **SELECT statement (via external tables or staging queries)**
  - Or **transformations after loading**

  ---

### Question  
Which views provide detailed storage breakdowns (Active, Time Travel, Fail-safe) and other useful metadata for Snowflake?


### Key Answer  
`SNOWFLAKE.ACCOUNT_USAGE.TABLE_STORAGE_METRICS`

### Important Views for SnowPro Exam

#### 🔹 Storage & Cost Analysis
- `ACCOUNT_USAGE.TABLE_STORAGE_METRICS`  
  - ACTIVE_BYTES  
  - TIME_TRAVEL_BYTES  
  - FAILSAFE_BYTES  
  - RETAINED_FOR_CLONE_BYTES  

- `ACCOUNT_USAGE.DATABASE_STORAGE_USAGE_HISTORY`  
  - Storage trends over time (daily)

- `ACCOUNT_USAGE.STORAGE_USAGE`  
  - Overall account storage usage


#### 🔹 Query & Performance
- `ACCOUNT_USAGE.QUERY_HISTORY` 
- `ACCOUNT_USAGE.QUERY_HISTORY_BY_*`  
- `INFORMATION_SCHEMA.QUERY_HISTORY()` (table function)


#### Warehouse Monitoring
- `ACCOUNT_USAGE.WAREHOUSE_LOAD_HISTORY` ✅  
- `ACCOUNT_USAGE.WAREHOUSE_METERING_HISTORY`


#### Tables & Metadata
- `ACCOUNT_USAGE.TABLES`  
- `INFORMATION_SCHEMA.TABLES`  


#### Data Loading & Pipes
- `ACCOUNT_USAGE.COPY_HISTORY`   
- `ACCOUNT_USAGE.LOAD_HISTORY`  
- `ACCOUNT_USAGE.PIPES`


#### Access & Security
- `ACCOUNT_USAGE.USERS`  
- `ACCOUNT_USAGE.ROLES`  
- `ACCOUNT_USAGE.GRANTS_TO_USERS`


### Exam Tips
- Detailed storage breakdown  **ONLY in ACCOUNT_USAGE.TABLE_STORAGE_METRICS**
- `INFORMATION_SCHEMA` = **current state (limited)**
- `ACCOUNT_USAGE` = **historical + detailed (exam favorite)**

---

### Question  
Can a task use Snowflake-managed compute if no warehouse is specified?

### Answer  
 True

### Summary
- If no warehouse is specified, the task uses **Snowflake-managed serverless compute**
- Snowflake **automatically provisions and scales resources**
- Simplifies task management (no warehouse needed)

---

### Question  
Which Snowflake table types do NOT have Fail-safe protection?

### Answer  
 Transient Tables  
 Temporary Tables  

### Overview

| Table Type  | Time Travel | Fail-safe |
|------------|------------|----------|
| Permanent  | 0–90 days  | ✅ 7 days |
| Transient  | 0–1 day    | ❌ 0 days |
| Temporary  | 0–1 day    | ❌ 0 days |


### Summary

- **Permanent Tables**
  - Have **Fail-safe (7 days)**
  - Higher storage cost
- **Transient Tables**
  - **No Fail-safe**
  - Used for staging/ETL data
- **Temporary Tables**
  - **No Fail-safe**
  - Session-based (auto-drop after session ends)


### Key Notes

- **Cloned tables inherit the source table type**
- Only **Permanent tables include Fail-safe**
- Fail-safe starts **after Time Travel expires**


### Why Other Options Are Incorrect

- **Permanent Tables** → DO have Fail-safe  
- **Cloned Tables** → Follow source table behavior (may or may not have Fail-safe)

---


### Question  
What does the VALIDATION_MODE parameter do in the COPY INTO command?

### Answer  
- Allows **validating data without loading it into the table**
- Performs a **dry run** to identify issues

---

### Summary
- No data is loaded when `VALIDATION_MODE` is used  
- Helps catch **errors before ingestion**

---

### Available Options

- `RETURN_n_ROWS` → Preview first *n* rows  
- `RETURN_ERRORS` → Show errors for files  
- `RETURN_ALL_ERRORS` → Show all errors across all files  


---

### Examples

```sql
-- Preview first 10 rows
COPY INTO my_table FROM @my_stage
  VALIDATION_MODE = 'RETURN_10_ROWS';

-- Validate and return errors
COPY INTO my_table FROM @my_stage
  VALIDATION_MODE = 'RETURN_ERRORS';
```

---

### Question  
Which table type is most appropriate for storing a large (500M rows), reproducible dataset that must be available across sessions with minimal storage cost?

### Answer  
Transient Table

### Summary
- Large dataset → needs **persistent table (not temporary)**
- No long-term protection needed → **avoid Fail-safe cost**
- Available across sessions → **not temporary**

### Comparison

| Table Type  | Time Travel | Fail-safe | Scope             | Cost     |
|------------|------------|----------|------------------|---------|
| Permanent  | 0–90 days  | ✅ 7 days | Persistent        | High    |
| Transient  | 0–1 day    | ❌ 0 days | Persistent        | ✅ Lower
| Temporary  | 0–1 day    | ❌ 0 days | Session-only      | Low     |


### Key Points
- **Transient tables** are ideal for:
  - ETL / staging data  
  - Reproducible datasets  
  - Cost optimization  


### Example

```sql
CREATE TRANSIENT TABLE stg_etl_results (
  id INT,
  value STRING,
  processed_at TIMESTAMP
);
```
---

## Snowflake Dynamic Data Masking

### Question  
Which Snowflake feature allows masking sensitive column data dynamically at query time based on user roles, without changing the stored data?

### Answer  
Dynamic Data Masking

### Summary
- Masks **column-level data at query runtime**
- Based on **user role (CURRENT_ROLE())**
- Does **not modify stored data**
- Available in **Enterprise Edition**


### Implementation Steps

#### Step 1 — Create Masking Policy
```sql
CREATE MASKING POLICY phone_mask AS (val VARCHAR)
  RETURNS VARCHAR ->
  CASE
    WHEN CURRENT_ROLE() IN ('MEDICAL_STAFF') THEN val
    ELSE 'XXX-XXX-XXXX'
  END;
  
ALTER TABLE patient_records 
  MODIFY COLUMN phone_number
  SET MASKING POLICY phone_mask;
```

### Key Points

- Policy evaluated at query runtime
- Reusable across multiple columns
- Can be removed using:

```sql
ALTER TABLE ... MODIFY COLUMN ... UNSET MASKING POLICY;
```

### Why Other Options Are Incorrect

- **External Tokenization**  
  Changes data before loading → violates requirement  

- **Row Access Policies**  
  Filter rows, not columns  

- **Secure Views**  
  Require separate object maintenance  
  Not applied directly to base table  