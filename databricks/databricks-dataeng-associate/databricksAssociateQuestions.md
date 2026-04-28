**Question 1:**
  In DLT* pipelines, You can stream data from other tables in the same pipeline by using the STREAM() function**. In this case, you must define a streaming table using the CREATE STREAMING TABLE syntax.
  CREATE STREAMING TABLE table_name
  AS SELECT * FROM STREAM(source_table)
  
  * Please note that **Delta Live Tables (DLT)** has been recently renammed to **Lakeflow Declarative Pipeline**, however, the current exam version may still refer to it as Delta Live Tables.
  
  ** A recent syntax update now also supports the use of STREAM as a keyword.
  
  CREATE STREAMING TABLE table_name
  AS SELECT *
  FROM STREAM source_table;

**Question 2:**
Establishes a streaming DataFrame in PySpark to read from this Kafka topic
  eventsStream = (spark.readStream
                      .format("kafka")
                      .option("kafka.bootstrap.servers", "host:port")
                      .option("subscribe", "events_topic")
                      .option("startingOffsets", "latest")
                      .load()
                  )
**Overall explanation**
  The correct approach uses PySpark’s readStream with format "kafka", and .option() methods to specify the necessary configurations, such as:
  kafka.bootstrap.servers to identify the Kafka cluster
  subscribe to define the target Kafka topic, and
  startingOffsets to control the consumption starting point. This can be set to "latest" or "earliest" to determine where to begin reading.

**Question 3:**
Job clusters have a maximum notebook output size of 30 MB.
**Question 4:**
How does Automatic Liquid Clustering determine which columns to use as clustering keys in a Unity Catalog-managed Delta table?
Overall explanation
Automatic Liquid Clustering in Databricks is a feature designed to automatically optimize the physical layout of data in Delta tables based on the access patterns and metadata statistics. It leverages Predictive Optimization, which uses query behavior analytics to select clustering keys dynamically.
**Question 5:**
Which scenario is best suited for using spot instances in Databricks? Spot instances are ideal for stateless, fault-tolerant workloads—such as batch processing jobs that can be retried without significant data loss or user impact. These jobs are often time-flexible and can tolerate interruptions, making them a good match for spot instances.
**Question 6:** Automatic Liquid Clustering is designed to dynamically adapt to evolving and unpredictable query patterns by continuously reorganizing data based on recent query filters. This is especially beneficial when query predicates frequently change across multiple columns, making static strategies like partitioning or Z-ordering less effective.

Partitioning works best when filters are stable and predictable, often on date/time columns. Z-ordering optimizes clustering for known high-cardinality columns with consistent filtering. When query filters are varied and unpredictable, Automatic Liquid Clustering provides the agility to improve performance without manual tuning.
**Question 7:** Cloudflare R2 removes egress costs, which helps significantly lower expenses when sharing data across cloud environments.
**Question 8:** In Delta Live Tables (DLT)*, views declared using the @dlt.view decorator are temporary objects. They only exist during a single pipeline run and are not saved to the catalog like tables. Because of this, views are useful for handling intermediate steps in data processing or performing quality checks when the results don’t need to be kept permanently.
In SQL, it’s is created using the following syntax:
CREATE TEMPORARY VIEW recent_orders
AS SELECT * FROM orders WHERE year > 2025;
* Please note that Delta Live Tables (DLT) has been recently renammed to Lakeflow Declarative Pipeline, however, the current exam version may still refer to it as Delta Live Tables.
**Question 9:** With ON VIOLATION FAIL UPDATE, records that violate the expectation will cause the pipeline to fail. When a pipeline fails because of an expectation violation, you must fix the pipeline code to handle the invalid data correctly before re-running the pipeline.
* Please note that Delta Live Tables (DLT) has been recently renammed to Lakeflow Declarative Pipeline, however, the current exam version may still refer to it as Delta Live Tables.

**Question 2:** Overall explanation
The resources section is where users define deployable objects, such as jobs, pipelines, notebooks, clusters, and more. These resources form the core of what is deployed when a bundle is applied to a target environment.



Options B, C, and D are incorrect:

settings is not a valid section in the context of a Databricks Asset Bundle.

pipelines allows you to create Lakeflow Declarative Pipelines (Delta Live Tables or DLT) pipelines

workflows are not used in the databricks.yml structure.
https://docs.databricks.com/aws/en/dev-tools/bundles/settings


**Question 2:**  spark.table() function returns the specified Spark SQL table as a PySpark DataFrame
**Question 2:**
**Question 2:**
v
**Question 2:**
**Question 2:**
**Question 2:**
  
