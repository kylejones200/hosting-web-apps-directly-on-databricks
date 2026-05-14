---
author: "Kyle Jones"
date_published: "September 18, 2025"
date_exported_from_medium: "November 10, 2025"
canonical_link: "https://medium.com/@kyle-t-jones/hosting-web-apps-directly-on-databricks-e932b7400c0c"
---

# Hosting Web Apps Directly on Databricks Three-tier architecture works because it separates concerns. The
presentation tier runs the interface. The application tier manages logic...

### Hosting Web Apps Directly on Databricks 

Three-tier architecture works because it separates concerns. The presentation tier runs the interface. The application tier manages logic. The data tier handles persistence. In the past, these tiers lived far apart. Applications ran on servers in one place. Data lived in another. Integration was brittle and often insecure.

Databricks changes this dynamic. It not only delivers the strongest possible data tier, but now also lets you host applications directly on the platform. This collapses distance between the tiers. It reduces latency. It improves governance. It simplifies the development of data-driven applications.

### Modern Three-Tier with Databricks
Here's how the architecture looks when you host apps alongside your data:


The presentation tier can still be external, but the application tier now runs in the same secure environment as the data. That means no raw data leaves Databricks.

### Why Host Apps on Databricks
- Lower Latency: Apps can query and score models without hopping between environments.
- Simplified Security: Unity Catalog policies apply across data and application access.
- Faster Development: Streamlit, Flask, or Dash apps can run directly in Databricks notebooks or Jobs.
- Operational Resilience: Apps inherit Databricks' cloud-scale reliability and failover.

### Example: Fraud Detection Dashboard
Let's walk through a simple example. Imagine a bank that wants to monitor transactions in real time.

#### Step 1: Stream data into Delta Lake
```python
from pyspark.sql.types import StructType, StringType, DoubleType, TimestampType

schema = StructType() \
    .add("transactionId", StringType()) \
    .add("userId", StringType()) \
    .add("amount", DoubleType()) \
    .add("timestamp", TimestampType())
stream = spark.readStream \
    .format("cloudFiles") \
    .option("cloudFiles.format", "json") \
    .schema(schema) \
    .load("/mnt/streaming/transactions")
stream.writeStream \
    .format("delta") \
    .option("checkpointLocation", "/mnt/checkpoints/transactions") \
    .outputMode("append") \
    .table("transactions")
```

#### Step 2: Train a fraud model
```python
from pyspark.ml.classification import RandomForestClassifier
from pyspark.ml.feature import VectorAssembler

# Prepare features
df = spark.table("transactions")
features = VectorAssembler(
    inputCols=["amount"], 
    outputCol="features"
).transform(df.withColumn("label", (df.amount > 1000).cast("int")))
# Train model
rf = RandomForestClassifier(featuresCol="features", labelCol="label")
model = rf.fit(features)
import mlflow
mlflow.spark.log_model(model, "fraud-model")
```

#### Step 3: Build a Streamlit app in Databricks
```python
import streamlit as st
import pandas as pd
from pyspark.sql import functions as F

st.title("Fraud Detection Dashboard")
# Query latest transactions
transactions = spark.sql("""
    SELECT transactionId, userId, amount, timestamp
    FROM transactions
    ORDER BY timestamp DESC
    LIMIT 20
""")
df = transactions.toPandas()
st.dataframe(df)
# Show flagged transactions
flagged = df[df['amount'] > 1000]
st.subheader("Flagged Transactions")
st.table(flagged)
```

This app runs directly in Databricks using Streamlit integration. It queries governed Delta tables and presents results instantly.

### Security and Governance
Unity Catalog enforces role-based access. Only the app service account can read the `transactions` table. Audit logs capture every query. No sensitive data moves outside Databricks.

### Extending the Architecture
This is a basic architecture for a basic app. But you can easily extend this by adding model scoring endpoints with Databricks Model Serving. You can build Flask APIs to expose fraud scores to external applications. Or you could deploy Dashboards that update in real time from Delta Live Tables. This is where devs get to be creative.

The three-tier structure still holds. What changes is the distance between the tiers. The data tier and application tier now share the same platform. That makes the architecture stronger, faster, and easier to operate. The three-tier app remains the gold standard. But by hosting the application tier in Databricks, you strengthen the model. You reduce latency, simplify governance, and deliver apps that are tightly coupled with your data and AI.
