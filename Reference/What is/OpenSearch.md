#product #project #search #k8s 

https://opensearch.org/

OpenSearch is an open-source, community-driven search and analytics suite derived from Elasticsearch 7.x and Kibana 7.x after their licenses changed. It includes a distributed search/analytics engine (OpenSearch) and a visualization/management layer (OpenSearch Dashboards). It is commonly used for log analytics, full-text search, observability data, and application search workloads. It remains fully Apache-2.0 licensed.

## Key Components

### OpenSearch (the engine)

- Distributed, horizontally scalable document store
- Near real-time indexing and search
- RESTful API
- Inverted-index search engine built on Lucene
- Supports structured and unstructured data
- In-cluster sharding and replication
- Works as a backend for logging, metrics, and search features

### OpenSearch Dashboards

- Visualization and dashboarding layer
- Querying, filtering, aggregations, and time-series exploration
- Plugin ecosystem for management, security, anomaly detection, ML, etc.
- UI for index templates, cluster settings, and alerting

## Core Features

### Search & Querying

- Full-text search
- Phrase, boolean, range, and fuzzy queries
- Highlighting
- Relevance ranking, BM25 scoring

### Analytics

- Aggregations (histograms, terms, cardinality, etc.)
- Time-series analysis
- Pipelines and transforms

### Observability

- Logs, metrics, traces ingestion and correlation
- Integrations with Fluent Bit, Logstash-compatible agents, and OpenTelemetry collectors

### Security (via OpenSearch Security plugin)

- Role-based access control
- Multi-tenant dashboards
- Encryption in transit and at rest
- LDAP/SAML/OIDC integration

### Machine Learning

- Anomaly detection
- k-NN vector search (HNSW, IVF, etc.)
- Semantic search support
- Model hosting for ML workloads

### Index management

- Index lifecycle policies (ILM-like)
- Hot/warm/cold tiers
- Snapshot/restore to S3, GCS, Azure, or local storage

## Operational Characteristics

### Deployment Models

- Self-managed (Kubernetes, VMs, Docker)
- Amazon OpenSearch Service (AWS managed offering)
- Fully open-source fork maintained by the OpenSearch Project

### Scalability & Performance

- Horizontal scaling through shard allocation
- Replica shards for high availability
- Routing and load balancing across data nodes
- Dedicated roles: data, master, ingest, machine-learning, coordinator

### Extensibility

- Plugin architecture for:
    - Alerting
    - Security
    - SQL / Piped Processing Language (PPL)
    - Anomaly detection
    - k-NN vector search

## Common Use Cases

- Log analytics (alternative to ELK stack)
- Security analytics (SIEM)
- Full-text search for applications
- Observability pipelines
- E-commerce search
- Vector search / semantic search
- Distributed metrics and trace storage

## Differences from Elasticsearch/Kibana

- Open-source Apache 2.0 licensing
- Community governance (OpenSearch Project)
- Additional native capabilities like k-NN search and PPL
- No license-locked features
- Backward compatibility with many Elasticsearch APIs (through 7.x lineage)
- Cannot form clusters with Elasticsearch (protocol divergence)

