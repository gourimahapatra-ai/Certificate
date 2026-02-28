## Databricks Associate
#### 1. What is Azure Databricks?</r>
#### 2. Data lakehouse
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
#### 27. 
#### 28. 
#### 29. 
#### 30.
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
#### 10. 
#### 10. 
#### 10. 
#### 10. 
#### 10.
