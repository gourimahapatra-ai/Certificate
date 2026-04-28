Creating Bronze Tables from CSV Files
-- Change the default catalog/schema
USE CATALOG dbacademy;
USE SCHEMA IDENTIFIER(DA.schema_name);

-- View current catalog and schema
SELECT 
  current_catalog(), 
  current_schema()

  --Display Database and statement to view available schemas in the dbacademy catalog
  SHOW SCHEMAS IN dbacademy; 

  --DESCRIBE SCHEMA EXTENDED statement to see information about your labuser schema (database) that was created for you within the dbacademy catalog. we are using the IDENTIFIER clause to dynamically reference your specific schema name in the lab
  DESCRIBE SCHEMA EXTENDED IDENTIFIER(DA.schema_name);

  -- DESCRIBE TABLE EXTENDED statement to describe the table
  DESCRIBE TABLE EXTENDED mydeltatable

  -- Volumes : Volumes are Unity Catalog objects that enable governance over non-tabular datasets
  DESCRIBE VOLUME dbacademy_ecommerce.v01.raw;

  -- List Files in a Volume
  LIST '/Volumes/dbacademy_ecommerce/v01/raw/users-historical'

-- Lab - Creating Bronze Tables from JSON Files

%python
spark.sql(f'''
          SELECT * 
          FROM json.`{DA.paths.working_dir}/json_demo_files/lab_kafka_events.json`
          ''').display()

--Create Bronze Table
CREATE OR REPLACE TABLE lab7_lab_kafka_events_raw
AS
SELECT 
  *,
  cast(unbase64(value) as STRING) as decoded_value
FROM read_files(
        DA.paths_working_dir || '/json_demo_files/lab_kafka_events.json',
        format => "json", 
        schema => '''
          key STRING, 
          timestamp DOUBLE, 
          value STRING
        ''',
        rescueddatacolumn => '_rescued_data'
      );

-- View the table
SELECT *
FROM lab7_lab_kafka_events_raw;

---- Parse the JSON formatted STRING
CREATE OR REPLACE TABLE lab7_lab_kafka_events_flattened_str
AS
SELECT 
  key,
  timestamp,
  decoded_value:user_id,
  decoded_value:event_type,
  cast(decoded_value:event_timestamp AS TIMESTAMP),
  from_json(decoded_value:items,'ARRAY<STRUCT<item_id: STRING, price_usd: DOUBLE, quantity: BIGINT>>') AS items
FROM lab7_lab_kafka_events_raw;


---- Display the table
SELECT *
FROM lab7_lab_kafka_events_flattened_str;

--
---- Return the structure of the JSON formatted string
SELECT schema_of_json(decoded_value)
FROM lab7_lab_kafka_events_raw
LIMIT 1;


---- Use the JSON structure from above within the from_json function to convert the JSON formatted string to a STRUCT
---- NOTE: The STRUCT decoded_value_struct column is included in this solution to display the column
CREATE OR REPLACE TABLE lab7_lab_kafka_events_flattened_struct
AS
SELECT
  key,
  timestamp,
  from_json(decoded_value, 'STRUCT<event_timestamp: STRING, event_type: STRING, items: ARRAY<STRUCT<item_id: STRING, price_usd: DOUBLE, quantity: BIGINT>>, user_id: STRING>') AS decoded_value_struct,
  decoded_value_struct.user_id,
  decoded_value_struct.event_type,
  cast(decoded_value_struct.event_timestamp AS TIMESTAMP),
  decoded_value_struct.items
FROM lab7_lab_kafka_events_raw;


---- Display the table
SELECT *
FROM lab7_lab_kafka_events_flattened_struct;

-----
---- Return the structure of the JSON formatted string
SELECT schema_of_json(decoded_value)
FROM lab7_lab_kafka_events_raw
LIMIT 1;


---- Use the JSON structure from above within the from_json function to convert the JSON formatted string to a STRUCT
---- NOTE: The STRUCT decoded_value_struct column is included in this solution to display the column
CREATE OR REPLACE TABLE lab7_lab_kafka_events_flattened_struct
AS
SELECT
  key,
  timestamp,
  from_json(decoded_value, 'STRUCT<event_timestamp: STRING, event_type: STRING, items: ARRAY<STRUCT<item_id: STRING, price_usd: DOUBLE, quantity: BIGINT>>, user_id: STRING>') AS decoded_value_struct,
  decoded_value_struct.user_id,
  decoded_value_struct.event_type,
  cast(decoded_value_struct.event_timestamp AS TIMESTAMP),
  decoded_value_struct.items
FROM lab7_lab_kafka_events_raw;


---- Display the table
SELECT *
FROM lab7_lab_kafka_events_flattened_struct;
-----
  --Data Ingestion with CREATE TABLE AS and COPY INTO
  SELECT current_catalog(), current_schema()
  LIST '/Volumes/dbacademy_ecommerce/v01/raw/users-historical'
  SELECT * FROM parquet.`/Volumes/dbacademy_ecommerce/v01/raw/users-historical`;

  --Batch Data Ingestion with CTAS and read_files() : The read_files() table-valued function (TVF) enables reading a variety of file formats and provides additional options for data ingestion.

  SELECT * 
FROM read_files(
  '/Volumes/dbacademy_ecommerce/v01/raw/users-historical',
  format => 'parquet'
)
LIMIT 10;

--Create table
-- Drop the table if it exists for demonstration purposes
DROP TABLE IF EXISTS historical_users_bronze_ctas_rf;


-- Create the Delta table
CREATE TABLE historical_users_bronze_ctas_rf 
SELECT * 
FROM read_files(
        '/Volumes/dbacademy_ecommerce/v01/raw/users-historical',
        format => 'parquet'
      );


-- Preview the Delta table
SELECT * 
FROM historical_users_bronze_ctas_rf 
LIMIT 10;

--DESCRIBE TABLE EXTENDED historical_users_bronze_ctas_rf;

Managed vs External Tables in Databricks
Managed Tables
Databricks manages both the data and metadata.
Data is stored within Databricks’ managed storage.
Dropping the table also deletes the data.
Recommended for creating new tables.
External Tables
Databricks only manages the table metadata.
Dropping the table does not delete the data.
Supports multiple formats, including Delta Lake.
Ideal for sharing data across platforms or using existing external data.

--C2. (BONUS) Python Ingestion
%python
# 1. Read the Parquet files from the volume into a Spark DataFrame
df = (spark
      .read
      .format("parquet")
      .load("/Volumes/dbacademy_ecommerce/v01/raw/users-historical")
    )


# 2. Write to the DataFrame to a Delta table (overwrite the table if it exists)
(df
 .write
 .mode("overwrite")
 .saveAsTable(f"dbacademy.{DA.schema_name}.historical_users_bronze_python")
)


## 3. Read and view the table
users_bronze_table = spark.table(f"dbacademy.{DA.schema_name}.historical_users_bronze_python")
users_bronze_table.display()

--Incremental Data Ingestion with COPY INTO
Example 1: Common Schema Mismatch Error
Example 2: Preemptively Handling Schema Evolution

--------------------------------------------
-- This cell returns an error
--------------------------------------------

-- Drop the table if it exists for demonstration purposes
DROP TABLE IF EXISTS historical_users_bronze_ci;


-- Create an empty table with the specified table schema (only 2 out of the 3 columns)
CREATE TABLE historical_users_bronze_ci (
  user_id STRING,
  user_first_touch_timestamp BIGINT
);


-- Use COPY INTO to populate Delta table
COPY INTO historical_users_bronze_ci
  FROM '/Volumes/dbacademy_ecommerce/v01/raw/users-historical'
  FILEFORMAT = parquet;

COPY INTO historical_users_bronze_ci
  FROM '/Volumes/dbacademy_ecommerce/v01/raw/users-historical'
  FILEFORMAT = parquet
  COPY_OPTIONS ('mergeSchema' = 'true');     -- Merge the schema of each file

SELECT *
FROM historical_users_bronze_ci
LIMIT 10;

Example 2: Preemptively Handling Schema Evolution
-- Drop the table if it exists for demonstration purposes
DROP TABLE IF EXISTS historical_users_bronze_ci_no_schema;


-- Create an empty table without the specified schema
CREATE TABLE historical_users_bronze_ci_no_schema;


-- Use COPY INTO to populate Delta table
COPY INTO historical_users_bronze_ci_no_schema
  FROM '/Volumes/dbacademy_ecommerce/v01/raw/users-historical'
  FILEFORMAT = parquet
  COPY_OPTIONS ('mergeSchema' = 'true');


D2. Idempotency (Incremental Ingestion)
COPY INTO tracks the files it has previously ingested. If the command is run again, no additional data is ingested because the files in the source directory haven't changed.

--Let's run the COPY INTO command again and check if any data is re-ingested into the table.
COPY INTO historical_users_bronze_ci_no_schema
  FROM '/Volumes/dbacademy_ecommerce/v01/raw/users-historical'
  FILEFORMAT = parquet
  COPY_OPTIONS ('mergeSchema' = 'true');

