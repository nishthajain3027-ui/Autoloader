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
