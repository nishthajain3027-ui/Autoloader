# Autoloader
Hands on experience on Autoloader in Databricks

A global retail organization collects customer profile updates from multiple operational
systems. Whenever a customer is created or updated, the source systems emit Change Data
Capture (CDC) events.
These events are delivered as JSON files into cloud storage in near real-time.
The data engineering team must build a robust pipeline using Databricks and PySpark that
ingests these CDC events, processes them incrementally, and maintains a historical
Customer Dimension table using Slowly Changing Dimension Type 2 (SCD2).
The pipeline must be designed using a Medallion Architecture:
Bronze → Silver → Gold
**Source Data**
Incoming JSON CDC events arrive in cloud storage at: /mnt/raw/customer_updates/

Each record represents an event from the operational system.

**Sample Records**

{"customer_id":"C101","name":"Rahul Sharma","email":"rahul@gmail.com","city":"Delhi","status":"ACTIVE","operation":"INSERT"," update_ts":"2026-03-01T09:00:00"} {"customer_id":"C102","name":"Priya Singh","email":"priya@gmail.com","city":"Mumbai","status":"ACTIVE","operation":"INSERT"," update_ts":"2026-03-01T09:05:00"} {"customer_id":"C101","name":"Rahul Sharma","email":"rahul@gmail.com","city":"Bangalore","status":"ACTIVE","operation":"UPDA TE","update_ts":"2026-03-02T10:00:00"} {"customer_id":"C102","name":"Priya Singh","email":"priya_new@gmail.com","city":"Mumbai","status":"ACTIVE","operation":"UPD ATE","update_ts":"2026-03-02T10:30:00"} {"customer_id":"C101","name":"Rahul Sharma","email":"rahul@gmail.com","city":"Bangalore","status":"INACTIVE","operation":"UP DATE","update_ts":"2026-03-03T12:00:00"}
**

# Requirements
**1. Bronze Layer**
Build a pipeline that:
● Ingests raw CDC JSON data using Auto Loader
● Stores raw records without modification
● Adds an ingestion timestamp
● Maintains schema evolution support

**2. Silver Layer**
Clean and validate the data.
Tasks:
● Convert update_ts into timestamp format
● Remove duplicate CDC events
● Validate that customer_id is not null
● Ensure operation values are valid:
INSERT
UPDATE
● Handle late arriving events

**3. Gold Layer – Customer Dimension**
Table
Create a Slowly Changing Dimension Type 2 table to maintain history.
The table should contain the following columns:
customer_sk (surrogate key)
customer_id
name
email
city
status
start_date
end_date
is_current

**4. SCD Type 2 Logic**
Implement the following logic:
New Customer
Insert a new record with:
start_date = update_ts
end_date = NULL
is_current = true
Customer Update
When attributes change:
1. Close previous record
end_date = update_ts
is_current = false
2. Insert new record
start_date = update_ts
is_current = true
Additional Constraints
Your solution must handle the following production challenges:
Out-of-order events
Events may arrive late.
Example:
update_ts = 2026-03-01
arrives after
update_ts = 2026-03-02
Your pipeline must still produce correct history.
Duplicate CDC events
Example duplicates may occur:
(customer_id , update_ts)
Duplicates must be removed.
Incremental processing
The pipeline must:
● Avoid full table recomputation
● Process only new CDC events

**Expected Output**
Final Customer Dimension Table
customer_sk | customer_id | city | status | start_date | end_date | is_current
---------------------------------------------------------------------------------------
1 | C101 | Delhi | ACTIVE | 2026-03-01 | 2026-03-02 | false
2 | C101 | Bangalore | ACTIVE | 2026-03-02 | 2026-03-03 | false
3 | C101 | Bangalore | INACTIVE | 2026-03-03 | NULL | true
4 | C102 | Mumbai | ACTIVE | 2026-03-01 | NULL | true
