🚕 NYC Taxi Data Engineering Pipeline

Batch Incremental + Kafka Streaming (Optional Extension)

📌 Overview

This project implements an end-to-end Data Engineering pipeline using Databricks, Apache Spark, and Azure Data Factory (ADF) following a Bronze–Silver–Gold architecture.

The pipeline supports:

Incremental batch ingestion using event-time watermarking

Idempotent processing with deduplication and MERGE

Business-ready Gold aggregations

An optional Kafka streaming ingestion path feeding the same Bronze layer

🏗️ Architecture

Batch Flow

Source Data
   ↓
Bronze (Raw Delta Table)
   ↓
Silver (Incremental + Deduplicated)
   ↓
Gold (Business Aggregations)


Streaming Extension

Kafka / Event Stream
        ↓
     Bronze


Azure Data Factory orchestrates execution and manages watermark state externally.

🧱 Layers Explained
🟫 Bronze Layer

Raw ingestion into Delta tables

Append-only

No transformations

Preserves all NULLs for traceability

🟪 Silver Layer

Incremental batch processing using event-time watermark

Safety window to handle late-arriving data

Deduplication using business keys:

(lpep_pickup_datetime, PULocationID, DOLocationID)


Idempotent MERGE logic to support safe reruns

🟨 Gold Layer

Business-ready aggregations

Vendor normalization (NULL → 'UNKNOWN')

Daily metrics such as:

Total trips

Total revenue

Average trip distance

Gold is optimized for analytics and reporting.

⏱️ Incremental Processing & Watermarking

Incremental column: lpep_pickup_datetime

Watermark represents the latest successfully processed event time

Watermark state is externalized (ADF / control table pattern)

Watermark advances only after successful runs

This ensures:

No duplicate processing

Safe reruns

Efficient batch execution

🔄 Kafka Streaming (Optional Extension)

Kafka ingestion implemented using Spark Structured Streaming

Uses availableNow trigger due to Databricks Serverless constraints

Writes streaming data into the same Bronze table

Downstream Silver & Gold logic remains unchanged

Note:
In Databricks Serverless, local Kafka brokers are not reachable due to network isolation.
This design assumes a managed Kafka-compatible service (e.g., Confluent Cloud or Azure Event Hubs).

🛠️ Tech Stack

Apache Spark (PySpark)

Databricks (Serverless)

Delta Lake

Azure Data Factory (Orchestration)

Apache Kafka (Conceptual / Optional Extension)

🎯 Key Learnings

Designing incremental batch pipelines with watermarks

Handling late-arriving data safely

Deduplication and idempotent MERGE patterns

Separation of concerns across Bronze, Silver, and Gold layers

Integrating batch and streaming ingestion paths
