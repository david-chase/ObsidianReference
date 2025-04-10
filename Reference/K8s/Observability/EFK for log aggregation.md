#fluent #kibana #elasticsearch #logging #k8s 

## Overview

A simple and effective solution for log aggregation and viewing in Kubernetes is deploying the **EFK stack**, which consists of:

- **Elasticsearch**: For storing and indexing logs.
- **Fluent Bit (or Fluentd)**: For collecting and forwarding logs.
- **Kibana**: For visualizing and querying logs.

## Component Breakdown

### Fluent Bit / Fluentd

- Runs as a **DaemonSet** on each node.
- Collects logs from containers via the container runtime (e.g., `containerd`).
- Forwards logs to Elasticsearch.
- **Fluent Bit** is preferred for lightweight, resource-efficient setups.

### Elasticsearch

- Centralized storage engine.
- Indexes logs and makes them searchable.

### Kibana

- Web-based UI.
- Allows filtering, searching, and visualizing logs.

## Deployment Using Helm

```powershell
# Add Bitnami repository
helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo update

# Deploy Elasticsearch
helm install elasticsearch bitnami/elasticsearch `
  --namespace logging `
  --create-namespace `
  --set persistence.enabled=true `
  --set podSecurityContext.fsGroup=1001 `
  --set containerSecurityContext.runAsUser=1001 `
  --set containerSecurityContext.runAsGroup=1001 `
  --set resources.requests.memory=512Mi `
  --set resources.requests.cpu=250m `
  --set resources.limits.memory=1Gi `
  --set resources.limits.cpu=500m

# Deploy Fluent Bit
helm install fluent-bit bitnami/fluent-bit --namespace logging

# Deploy Kibana
helm install kibana bitnami/kibana `
  --namespace logging `
  --set elasticsearch.hosts[0]=elasticsearch `
  --set elasticsearch.port=9200 `
  --set resources.requests.memory=512Mi `
  --set resources.requests.cpu=250m
```

## Access and Usage

- Expose Kibana using port-forward or an ingress controller.
- Use Kibana to query and visualize logs.
- Configure Fluent Bit to parse logs correctly, typically from `/var/log/containers/`.

## Benefits

- Centralized log management.
- Easy filtering and visualization.
- Fast and lightweight with Fluent Bit.

## Optional Enhancements

- Add persistent storage to Elasticsearch.
- Use authentication with Kibana.
- Apply log filters/parsers in Fluent Bit for structure.