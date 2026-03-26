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
