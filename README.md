# Yelp Dataset - Data Engineering Assessment

## Project Overview
This repository contains an end-to-end Data Engineering pipeline designed to process the Yelp Dataset. The objective is to ingest raw JSON data, model it for Business Intelligence (BI) purposes, and answer specific business questions using SQL, such as identifying "Rising Star" businesses.

The entire pipeline was developed using **Databricks**, **PySpark**, and **Delta Lake**, following best practices for scalable data architectures.

## Architecture & Data Modeling
The project follows the **Medallion Architecture** to ensure data quality and reliability:

1. **Bronze Layer (Raw):** Ingestion of raw `.json` files directly from the storage volume.
2. **Silver Layer (Curated):** Data is cleaned, deduplicated, and strongly typed. 
3. **Gold Layer (Business-ready):** The final tables form a **Star Schema** designed for the BI team.
   * **Fact Table:** `fact_reviews` (Contains metrics like stars and review dates).
   * **Dimension Tables:** `dim_business` and `dim_users` (Contains descriptive attributes).

### BI Scenarios Supported
This Star Schema empowers the BI team to easily build dashboards for various use cases:
* **Regional Sentiment Analysis:** Correlating business locations (City/State in `dim_business`) with their average ratings.
* **User Engagement & Churn:** Analyzing `yelping_since` and `review_count` in `dim_users` to track if veteran users are leaving fewer reviews over time.

## Scalability & Performance
If deployed in a production environment, this pipeline is designed to scale under peak and off-peak loads:
* **Storage:** Data is stored in **Delta format**, which provides ACID transactions and allows for techniques like `Z-Ordering` (e.g., optimizing by `business_id`) to significantly speed up complex JOIN operations.
* **Compute:** Utilizing Databricks Autoscaling clusters ensures that worker nodes dynamically spin up during heavy data transformations (peak) and scale down to zero when idle (off-peak) to manage costs.
* **Monitoring:** The Spark UI is used to monitor DAGs, detect data skewness, and prevent memory spillage. Unity Catalog can be leveraged for data lineage and governance.

## How to Run this Project

**1. Prerequisites**
* A Databricks Workspace (The free Community/Free Edition is sufficient).
* The Yelp Dataset (Download the `.json` files from [Kaggle](https://www.kaggle.com/datasets/yelp-dataset/yelp-dataset)).

**2. Setup**
1. Create a Volume in your Databricks Unity Catalog named `raw_files` (e.g., `/Volumes/main/default/raw_files/`).
2. Upload the `yelp_academic_dataset_business.json`, `yelp_academic_dataset_user.json`, and `yelp_academic_dataset_review.json` files to this volume.
3. Import the `Yelp_Data_Pipeline.ipynb` notebook into your Databricks workspace.

**3. Execution**
* Attach the notebook to a running compute cluster (Serverless or All-purpose).
* Click **"Run All"**.
* The notebook will automatically execute the Bronze ingestion, Silver transformations, Gold modeling, and output the final SQL query results for the "Rising Stars" scenario.
