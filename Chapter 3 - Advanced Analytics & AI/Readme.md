## Guide to Real-Time Data and Advanced Analytics

This guide covers methodologies for working with data science workflows and handling streaming data for real-time analysis.

### Real-Time Data Handling

These modules focus on capturing, analyzing, and acting upon streaming data as it arrives.

* **Real Time Intelligence**: Utilize tools designed for immediate data capture and analysis. This approach allows for quick responses to new information.
* **Real Time Analytics Eventstream**: Set up a pipeline to continuously capture, transform, and route streaming data. This service is key for processing high-volume event data.
* **Data Activator**: Define conditions and patterns in incoming data to trigger automated actions. This feature helps in responding to specified data changes in real time.
* **Query Data in KQL Database**: Use the **Kusto Query Language (KQL)** to retrieve and analyze data stored in a KQL database. This is optimized for fast, exploratory analysis of large datasets.

### Data Science Workflow

The following steps outline the process of preparing data, training models, and deploying predictions.

* **Ingest Notebooks**: Utilize **notebooks** for the execution of code, data manipulation, and workflow organization. Notebooks serve as the primary environment for data science tasks.
* **Data Science Get Started**: Begin by setting up the necessary environment and importing tools for a data science project. This initial step establishes the workspace.
* **Data Science Explore Data**: Conduct initial data inspection to identify patterns, anomalies, and data quality issues. Visualizations are often used in this stage.
* **Data Science Preprocess Data Wrangler**: Use specialized tools to clean, transform, and prepare raw data for model training. The goal is to format data for optimal model input.
* **Data Science Train**: Build and train machine learning models using prepared data. The training process involves fitting the model to the data to learn predictive patterns.
* **Data Science Batch**: Apply the trained model to a large volume of new, incoming data. This process generates predictions or insights in a non-real-time, scheduled manner.

[7. Real Time Intelligence.md](https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%202%20-%20Data%20Preparation%20%26%20Transformation/7.%20Real%20Time%20Intelligence.md) Set up Fabric Real Time Intelligence hub and connect event sources.

[8. Data Science Get Started.md](https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%202%20-%20Data%20Preparation%20%26%20Transformation/8.%20Data%20Science%20Get%20Started.md) Provision a data science workspace and link it to your lakehouse.

[8.1 Data Science Explore Data.md](https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%202%20-%20Data%20Preparation%20%26%20Transformation/8.1%20Data%20Science%20Explore%20Data.md) Profile datasets with descriptive statistics and visual checks.

[8.2 Data Science Preprocess Data Wrangler.md](https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%202%20-%20Data%20Preparation%20%26%20Transformation/8.2%20Data%20Science%20Preprocess%20Data%20Wrangler.md) Apply cleansing steps and generate reusable PySpark code automatically.

[8.3 Data Science Train.md](https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%202%20-%20Data%20Preparation%20%26%20Transformation/8.3%20Data%20Science%20Train.md) Train and evaluate ML models using Synapse ML and open source libraries.

[8.4 Data Science Batch.md](https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%202%20-%20Data%20Preparation%20%26%20Transformation/8.4%20Data%20Science%20Batch.md) Deploy a scheduled notebook to score new data and write predictions back.

[9 Real Time Analytics Eventstream.md](https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%202%20-%20Data%20Preparation%20%26%20Transformation/9%20Real%20Time%20Analytics%20Eventstream.md) Build an Eventstream to ingest and transform streaming events on the fly.

[10. Ingest Notebooks.md](https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%202%20-%20Data%20Preparation%20%26%20Transformation/10.%20Ingest%20Notebooks.md) Use PySpark notebooks to stream or batch load data from external sources.

[11 Data Activator.md](https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%202%20-%20Data%20Preparation%20%26%20Transformation/11%20Data%20Activator.md) Configure no code alerts and actions when data meets defined conditions.

[12. Query Data in KQL Database.md](https://github.com/Balasubramanian-pg/Fabric-Data-Engineering-Projects/blob/main/Chapter%202%20-%20Data%20Preparation%20%26%20Transformation/12.%20Query%20Data%20in%20KQL%20Database.md) Write Kusto queries to explore high velocity streaming data.
