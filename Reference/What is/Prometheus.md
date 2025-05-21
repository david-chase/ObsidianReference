#observability #metrics #product #k8s 

https://prometheus.io/

Prometheus is an **open-source systems monitoring and alerting toolkit**, originally built at **SoundCloud** in 2012 and now part of the **Cloud Native Computing Foundation (CNCF)**, alongside Kubernetes.

### Key Features of Prometheus:

1. **Time Series Database (TSDB)**:
    
    - Stores all data as time series (metrics with timestamp and labels).
        
    - Highly efficient for recording metrics data.
        
2. **Pull-based Metrics Collection**:
    
    - Prometheus **scrapes (pulls)** metrics from endpoints (`/metrics` HTTP path) rather than relying on agents pushing data.
        
    - This pull model gives Prometheus control over data collection frequency and targets.
        
3. **Powerful Query Language (PromQL)**:
    
    - Allows for flexible and expressive querying of time-series data.
        
    - Used for building dashboards, alerts, and ad-hoc analysis.
        
4. **Multi-dimensional Data Model**:
    
    - Metrics are identified by a name and a set of **key-value pairs (labels)**.
        
    - Enables rich querying and filtering of metrics.
        
5. **Alerting**:
    
    - Prometheus supports defining **alert rules**.
        
    - Alerts are sent to an **Alertmanager** component, which can route, deduplicate, silence, and notify (e.g., email, Slack, PagerDuty).
        
6. **Visualization**:
    
    - Has a built-in basic web UI.
        
    - Often integrated with **Grafana** for advanced dashboards and visualizations.
        
7. **Federation & Scalability**:
    
    - Supports **federated setups** for large-scale deployments.
        
    - Not designed for long-term storage (uses local disk), but can be integrated with remote storage systems.