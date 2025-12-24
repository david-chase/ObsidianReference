

https://airflow.apache.org/

Apache Airflow is an open-source workflow orchestration platform used to programmatically author, schedule, and monitor data pipelines. It models workflows as Directed Acyclic Graphs (DAGs), where each node represents a task and edges define dependencies. Airflow is widely used for data engineering, analytics pipelines, ML workflows, and infrastructure automation.

Airflow is a project of the Apache Software Foundation.

## Core concepts

- DAG (Directed Acyclic Graph)
    - A Python-defined workflow that describes task order and dependencies
    - Version-controlled like regular code
- Task
    - A single unit of work (e.g., run a SQL query, call an API, execute a script)
- Operator
    - A template for a task (e.g., PythonOperator, BashOperator, KubernetesPodOperator)
- Scheduler
    
    - Determines when and in what order tasks should run
        
- Executor
    
    - Defines how tasks are executed (Local, Celery, Kubernetes, etc.)
        
- Metadata database
    
    - Stores DAG state, task history, schedules, and metadata
        
- Web UI
    
    - Visualizes DAGs, task dependencies, execution history, and logs
        

## Key features

- Workflow-as-code using Python
    
- Explicit dependency management (no hidden execution order)
    
- Rich scheduling (cron-style, event-based, manual triggers)
    
- Retries, timeouts, SLAs, and alerting
    
- Extensible plugin and provider ecosystem
    
- Strong observability via UI and logs
    
- Backfilling and re-running historical workflows
    

## Execution models

- LocalExecutor
    
    - Single-node execution, useful for development
        
- CeleryExecutor
    
    - Distributed execution using a message broker (e.g., Redis, RabbitMQ)
        
- KubernetesExecutor
    
    - Each task runs as its own Kubernetes pod
        
- Hybrid (Celery + Kubernetes)
    
    - Mix of containerized and worker-based execution
        

## Common use cases

- ETL / ELT data pipelines
    
- Data warehouse loading and transformation
    
- ML training and batch inference orchestration
    
- Periodic API ingestion jobs
    
- Infrastructure and platform automation
    
- Coordinating multi-step jobs across systems
    

## What Airflow is not

- Not a streaming engine (it orchestrates jobs; it does not process data itself)
    
- Not a general-purpose task queue
    
- Not event-first by default (primarily schedule-driven, though sensors and triggers exist)
    

## Typical stack integrations

- Databases: PostgreSQL, MySQL, Snowflake, BigQuery
    
- Data tools: Spark, dbt, Hadoop
    
- Cloud services: AWS, GCP, Azure
    
- Containers: Docker and Kubernetes
    
- Messaging: Slack, email, PagerDuty
    

## Why teams use Airflow

- Clear, explicit workflow definitions
    
- Excellent visibility into pipeline state and failures
    
- Mature ecosystem and large community
    
- Strong fit for batch-oriented, dependency-heavy workflows