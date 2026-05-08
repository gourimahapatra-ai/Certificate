# Snowflake SnowPro Core Certification (COF-C03) – Complete Preparation Guide

## 📊 Exam Weightage

| Section | Topic | Weight |
|--------|------|--------|
| 1 | Features & Architecture of Snowflake AI Data Cloud | 31% |
| 2 | Account Management & Data Governance | 20% |
| 3 | Loading, Unloading, and Connecting Data | 18% |
| 4 | Performance Optimization, Queries, Transformations | 21% |
| 5 | Data Collaboration | 10% |

# 🔵 1. Features & Architecture (31%)

## 1.1 What is Snowflake
- Cloud-native data platform (OK)
- Supports:
  - Data Warehousing
  - Data Lake
  - Data Engineering
  - AI/ML workloads

## 1.2 Snowflake Architecture

### 3-Layer Architecture

#### 1. Storage Layer
- Stores data in micro-partitions
- Columnar format
- Automatic compression

#### 2. Compute Layer (Virtual Warehouses)
- Independent compute clusters
- MPP processing
- Auto scale / auto suspend

#### 3. Cloud Services Layer
- Metadata management
- Query optimization
- Authentication & access control

## 1.3 Key Features
- Separation of compute & storage
- Zero-copy cloning
- Time Travel
- Fail-safe
- Data sharing
- Semi-structured data support (JSON, Parquet, Avro)

## 1.4 Micro-partitions
- Automatic partitioning
- Immutable
- Stores metadata (min/max values)

**Benefits:**
- Partition pruning
- Query performance improvement

## 1.5 Snowflake AI Data Cloud
- Supports:
  - AI/ML
  - Data pipelines
  - Data apps
- Integrates with Spark, BI tools

# 🟢 2. Account Management & Data Governance (20%)

## 2.1 Account Structure
- Organization → Accounts → Databases → Schemas → Tables

## 2.2 RBAC (Role-Based Access Control)

### Key Roles
- ACCOUNTADMIN
- SYSADMIN
- SECURITYADMIN
- USERADMIN

**Concept:**
- Roles → Assigned to Users
- Privileges → Assigned to Roles

## 2.3 Object Hierarchy
Organization
→ Account
→ Database
→ Schema
→ Table/View

## 2.4 Data Governance

### Access Control
- GRANT / REVOKE privileges

### Data Protection
- Time Travel → Recover past data
- Fail-safe → Disaster recovery (7 days)

### Policies
- Dynamic Data Masking
- Row Access Policy

### Authentication
- SSO
- MFA
- OAuth

# 🟡 3. Loading, Unloading & Connecting Data (18%)

## 3.1 Data Loading

### Bulk Loading
- `COPY INTO` command
- Sources:
  - S3
  - Azure Blob
  - GCS

### Continuous Loading (Snowpipe)
- Auto ingestion
- Event-driven loading

## 3.2 Stages
- Internal Stage
- External Stage

## 3.3 File Formats
- CSV
- JSON
- Parquet
- Avro
- ORC

## 3.4 Data Unloading
- `COPY INTO <location>`
- Export to cloud storage

## 3.5 Connectivity
- JDBC / ODBC
- Python Connector
- BI Tools (Power BI, Tableau)

# 🔴 4. Performance Optimization & Queries (21%)

## 4.1 Virtual Warehouse Tuning
- Resize warehouse
- Multi-cluster warehouses
- Auto suspend/resume

## 4.2 Query Optimization

### Pruning
- Uses micro-partition metadata

### Clustering
- Define clustering keys
- Improves query performance

### Caching
- Result Cache
- Metadata Cache

### Query Profile
- Execution details
- Performance bottlenecks

## 4.3 Transformations
- SQL transformations
- Joins
- Aggregations
- Window functions


## 4.4 Semi-Structured Data
- VARIANT data type
- Query JSON using `:` operator


## 4.5 Materialized Views
- Precomputed results
- Faster queries


# 🟣 5. Data Collaboration (10%)

## 5.1 Secure Data Sharing
- Share data without copying
- Real-time access


## 5.2 Data Marketplace
- Share and consume datasets

## 5.3 Reader Accounts
- Share data with non-Snowflake users


## 5.4 Data Exchange
- Private data sharing platform

# 📅 Study Plan

## Week 1
- Architecture & Features
- Practice MCQs

## Week 2
- RBAC & Governance
- Hands-on practice

## Week 3
- Data Loading & Snowpipe
- Practice COPY INTO

## Week 4
- Performance & Query Optimization
- Scenario-based questions

## Final Revision (3–5 Days)
- Mock tests
- Focus on:
  - Micro-partitions
  - RBAC
  - Snowpipe
  - Caching


# 🎯 Exam Tips

- Focus on concepts, not syntax
- Expect scenario-based questions
- Understand differences:
  - Time Travel vs Fail-safe
  - Internal vs External Stage
  - Warehouse vs Database

# ✅ Key Topics to Master

- Micro-partitions
- RBAC
- Snowpipe
- Query Caching
- Clustering
- Data Sharing

**Good Luck 🚀**
