## Databricks Associate
#### 1. What is Azure Databricks?
#### 2. Data lakehouse/lakehouse platform<img width="766" height="413" alt="image" src="https://github.com/user-attachments/assets/fa10f48c-c82b-4b1b-b9d7-117cb5a8c50e" />
<img width="1132" height="667" alt="image" src="https://github.com/user-attachments/assets/b2487446-5329-4c24-883b-8d6b837449ed" />


#### 3. Delta Lake (ACID transactions and schema enforcement)
#### 4. Delta Sharing
#### 5. MLFlow
#### 6. Redash
#### 7. Unity Catalog (a unified, fine-grained governance solution for data and AI)
#### 8. Structured Streaming
#### 9. Spark
#### 10. Medallion architecture
#### 11. Databricks lakehouse uses two additional key technologies(Delta Lake and Unity Catalog)
#### 12. Delta Tables
#### 13. A schema-on-write approach, combined with Delta schema evolution capabilities
#### 14. Capabilities of a Databricks lakehouse
<ol>
  <li>Real-time data processing</li>
  <li>Data integration</li>
  <li>Schema evolution</li>
  <li>Data transformations</li>
  <li>Data analysis and reporting</li>
  <li>Machine learning and AI</li>
  <li>Data versioning and lineage</li>
  <li>Data governance</li>
  <li>Data sharing</li>
  <li>Operational analytics</li>
</ol>

#### 15.Lakehouse vs Data Lake vs Data Warehouse
#### 16. Databricks components
<ol>
  <li>Billing: Databricks units (DBUs)</li>
  <li>Authentication and authorization</li>
  <li>User</li>
  <li>Service Pricipal</li>
  <li>Group</li>
  <li>Access control list (ACL)</li>
  <li>Personal access token (PAT)</li>
  <li>Azure Databricks interfaces</li>
  <li>Databricks UI</li>
  <li>REST API</li>
  <li>SQL REST API</li>
  <li>CLI</li>
  <li>Unity Catalog</li>
  <li>Catalog</li>
  <li>Schema, Table, View, Volume</li>
  <li>Delta Table/ Table</li><ol><li>Managed</li><li>External</li><li>Foreign</li><li>Temporary</li></ol>
  <li>Catalog Explorer</li>
  <li>DBFS root</li>
  <li>Cluster</li>
  <li>Pool</li>
  <li>Databricks runtime</li>
  <li>Jobs & Pipelines UI</li>
  <li>Workload</li>
  <li>Execution Context</li>
  <li>Data Engg.</li>
  <li>Workspace</li>
  <li>Notebook</li>
  <li>Library</li>
  <li>Git Folder (Formerly Repos)</li>
  <li>AI and Machine Learning</li>
  <li>Mosaic AI</li>
  <li>ML Run Time</li>
  <li>Experiment</li>
  <li>Gen AI Model</li>
  <li>Model Registry</li>
  <li>Model Serving</li>
  <li>Data Warehouse</li>
  <li>Query History</li>
  <li>Visualization</li>
  <li>Dashboard</li>
  <li>CICD</li>
  <li>Metastore</li> - Unity Catalog provides an account-level metastore that registers metadata about data, AI, and permissions about catalogs, schemas, and tables. Azure Databricks provides a legacy Hive metastore for customers that have not adopted Unity Catalog.
  <ol>
    <li>Service Credential</li>
    <li>Storage Credential</li>
    <li>External Location</li>
    <li>External Metada</li>
    <li>Catalog</li><ol><li>Schema</li><ol><li>Table</li><li>View</li><li>Volume</li><li>Function/Model</li></ol></ol>
    <li>Share</li>
    <li>Recipient</li>
    <li>Provider</li>
    <li>Connection</li>
    <li>Clean Room</li>
  </ol>
</ol>

#### 17. Load CSV data into a DataFrame df = spark.read.csv(file,header=True,inferSchema=True,sep=",")
#### 18. df.withColumnRenamed("First Name", "First_Name")
#### 19. dfRenamedColumn.printSchema()
#### 20. df.write.mode("overwrite").saveAsTable(f"{path_table}" + "." + f"{table_name}")
#### 21. MCP Server.
#### 22. Unity Catalog vs. legacy Hive metastore
#### 23. Catalog types  : Standard catalog : Foreign catalog(Lakehouse Federation scenarios.)
#### 24. Workspace-catalog binding
#### 25. DROP SCHEMA inventory_schema CASCADE
#### 26. Volumes : When you work with volumes, you must use a SQL warehouse or a cluster running Databricks Runtime
#### 27. Architecture 
Account
Workspace
Control Place : Present In databricks Account
Compute Plane : Serverless and Classic Computeplane
<img width="857" height="587" alt="image" src="https://github.com/user-attachments/assets/fd50e76a-7dc1-48c7-b155-9220cbda028f" />
<img width="860" height="715" alt="image" src="https://github.com/user-attachments/assets/4f2177ea-8185-41a2-95ee-3eb6c0faf678" />

Unity Catalog Metastore
#### 28. ACID 
Atomicity means that all transactions either succeed or fail completely.
Data files are not tracked unless the transaction log records a new version. During a transaction, data files are written to the file directory backing the table. When the transaction completes, a new entry is committed to the transaction log that includes the paths to all files written during the transaction

Consistency guarantees relate to how a given state of the data is observed by simultaneous operations. Delta Lake uses optimistic concurrency control to provide transactional guarantees between writes. writes operate in three stages.Read: Reads (if needed) the latest available version of the table to identify which files need to be modified (that is, rewritten). Write: Writes data files to the directory used to define the table. and Validate and commit: Checks whether the proposed changes conflict with any other changes that may have been concurrently committed since the snapshot that was read.
If there are no conflicts, all the staged changes are committed as a new versioned snapshot, and the write operation succeeds.
If there are conflicts, the write operation fails with a concurrent modification exception. This failure prevents corruption of data.

Isolation refers to how simultaneous operations potentially conflict with one another.

Durability means that committed changes are permanent. databricks uses cloud storage has high availability and durability, Because transactions either succeed or fail completely and the transaction log lives alongside data files in cloud object storage, tables on Azure Databricks inherit the durability guarantees of the cloud object storage on which they're stored

#### 29. snapshot isolation on reads 
#### 30. write-serializable isolation on writes
#### 31. MERGE INTO :  inserts, updates, and deletes against a table into a single write transaction
#### 32. Does Delta Lake support multi-table transactions?
#### 33. Primary key and foreign key relationships on Azure Databricks are informational and not enforced
#### 34. Delta Lake prevents data corruption when multiple clusters write to the same table concurrently
#### 35. I modify a Delta table from different workspaces
#### 36. Medallion lakehouse architecture.
#### 37. Bronze (raw)
#### 38. Silver (validated) : Enforce data quality
  The following operations are performed in silver tables:
Schema enforcement
Handling of null and missing values
Data deduplication
Resolution of out-of-order and late-arriving data issues
Data quality checks and enforcement
Schema evolution
Type casting
Joins
#### 39. Gold (enriched) : 
Consists of aggregated data tailored for analytics and reporting.
Aligns with business logic and requirements.
Is optimized for performance in queries and dashboards.
#### 40. Multi-hop architectures <img width="769" height="407" alt="image" src="https://github.com/user-attachments/assets/bab5d5c2-dc0c-46b1-a20e-32a2f84c26a8" />
#### 41. Start modeling data : How to represent heavily nested or semi-structured data.
Use VARIANT data type.
Use JSON strings.
Create structs, maps, and arrays.
Flatten schema or normalize data into multiple tables.
#### 42. Continuous incremental ingestion, Higher cost, Latency Lower, Spark.readStrem
#### 43. Triggered incremental ingestion, Lower cost, Higher Latency. Ingest same way as previous one 
#### 44. Batch ingestion with manual incremental ingestion and Lower cost, Higher Latency
#### 45. Single source of truth
#### 46. liquid clustering for tables : liquid clustering for tables : Liquid clustering applies to both streaming tables and materialized views. In Databricks Runtime 15.4 LTS and above, you can enable automatic liquid clustering for Unity Catalog managed Delta tables

  <li>Tables that are often filtered by high cardinality columns.</li>
  <li>Tables that have skew in data distribution.</li>
  <li>Tables that grow quickly and require maintenance and tuning effort.</li>
  <li>Tables that have concurrent write requirements.</li>
  <li>Tables that have access patterns that change over time.</li>
  <li>Tables where a typical partition key could leave the table with too many or too few partitions.</li>
  <li>Force reclustering for all records : OPTIMIZE table_name FULL;</li>
  <li>Change clustering keys : ALTER TABLE table_name CLUSTER BY (new_column1, new_column2);</li>
#### 47. Which scenario is best suited for using spot instances in Databricks?
#### 48. 
#### 49. 
#### .
#### 10. 
#### 10. 
#### 10. 
#### 10. 
#### 10.
#### 10. 
#### 10. 
#### 10. 
#### 10. 
#### 10.
#### 10. 
#### 10. 
#### 10. 
#### 10. 
#### 10.
