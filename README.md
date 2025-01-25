# ETL Pipeline using dbt-core, Snowflake, and Astronomer Cosmos

## Overview
This project demonstrates how to build an ETL pipeline by integrating `dbt-core` with `Snowflake` and using `astronomer-cosmos` to orchestrate the pipeline in Apache Airflow.

## Architecture
The pipeline follows these steps:
1. **Extract**: Data is loaded into Snowflake from various sources.
2. **Transform**: `dbt-core` runs transformations in Snowflake using SQL models.
3. **Load**: The transformed data is materialized as tables/views in Snowflake.
4. **Orchestration**: `astronomer-cosmos` manages dbt execution within an Airflow DAG.

## Tech Stack
- **dbt-core**: Data transformation tool for SQL-based modeling.
- **Snowflake**: Cloud-based data warehouse.
- **Apache Airflow**: Workflow orchestration platform.
- **astronomer-cosmos**: Integration between dbt and Airflow.

## Installation
To set up the project locally, follow these steps:

1. Clone the repository:
   ```bash
   git clone https://github.com/your-repo.git
   cd your-repo
   ```

2. Create a virtual environment and install dependencies:
   ```bash
   python -m venv venv
   source venv/bin/activate  # On Windows use `venv\Scripts\activate`
   pip install -r requirements.txt
   ```

3. Configure Snowflake credentials in `profiles.yml` (used by dbt):
   ```yaml
   default:
     target: dev
     outputs:
       dev:
         type: snowflake
         account: "your-account"
         user: "your-username"
         password: "your-password"
         role: "your-role"
         database: "your-database"
         warehouse: "your-warehouse"
         schema: "your-schema"
         threads: 4
   ```

4. Initialize a dbt project (if not already set up):
   ```bash
   dbt init my_dbt_project
   ```

## Setting Up Airflow with Astronomer Cosmos
1. Install Airflow and `astronomer-cosmos`:
   ```bash
   pip install apache-airflow astronomer-cosmos-airflow
   ```

2. Define the Airflow DAG to run dbt models:
   ```python
   from airflow import DAG
   from cosmos.providers.dbt.core.dag import DbtDag
   from datetime import datetime

   default_args = {
       "owner": "airflow",
       "start_date": datetime(2024, 1, 1),
   }

   with DAG("dbt_etl_pipeline", default_args=default_args, schedule_interval="@daily") as dag:
       dbt_dag = DbtDag(
           project_dir="/path/to/your/dbt_project",
           profiles_dir="/path/to/your/profiles.yml",
           profile="default",
           target="dev",
       )
   ```

3. Deploy and start Airflow:
   ```bash
   airflow db init
   airflow webserver &
   airflow scheduler
   ```

## Running the Pipeline
- Run dbt manually:
  ```bash
  dbt run
  ```
- Trigger the DAG in Airflow:
  ```bash
  airflow dags trigger dbt_etl_pipeline
  ```

## Monitoring and Debugging
- Use `dbt debug` to check configuration:
  ```bash
  dbt debug
  ```
- View DAG execution in the Airflow UI.
- Check Snowflake logs for query performance.

## Conclusion
This setup enables scalable and automated ETL processing using dbt, Snowflake, and Airflow. `astronomer-cosmos` simplifies the integration between dbt and Airflow, making the pipeline easier to manage and schedule.

