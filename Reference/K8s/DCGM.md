#dgcm #prometheus #metrics #nvidia #k8s 

## Deploying the NVIDIA Operator

https://docs.nvidia.com/datacenter/cloud-native/gpu-operator/latest/getting-started.html

## Setting up Prometheus

https://docs.nvidia.com/datacenter/cloud-native/gpu-telemetry/latest/kube-prometheus.html

#### Modifying the AIO chart values.yaml

``` yaml
      scrape_configs:
        - job_name: prometheus
          static_configs:
            - targets:
              - localhost:9090
        - job_name: 'kubernetes-apiservers'
          kubernetes_sd_configs:
            - role: endpoints
          scheme: https
          tls_config:
            ca_file: /var/run/secrets/kubernetes.io/serviceaccount/ca.crt
          bearer_token_file: /var/run/secrets/kubernetes.io/serviceaccount/token
          relabel_configs:
            - source_labels: [__meta_kubernetes_namespace, __meta_kubernetes_service_name, __meta_kubernetes_endpoint_port_name]
              action: keep
              regex: default;kubernetes;https
        - job_name: 'kubernetes-nodes'
          scheme: https
          tls_config:
            ca_file: /var/run/secrets/kubernetes.io/serviceaccount/ca.crt
          bearer_token_file: /var/run/secrets/kubernetes.io/serviceaccount/token
          kubernetes_sd_configs:
            - role: node
          relabel_configs:
            - action: labelmap
              regex: __meta_kubernetes_node_label_(.+)
            - target_label: __address__
              replacement: kubernetes.default.svc:443
            - source_labels: [__meta_kubernetes_node_name]
              regex: (.+)
              target_label: __metrics_path__
              replacement: /api/v1/nodes/$1/proxy/metrics
        - job_name: 'kubernetes-nodes-cadvisor'
          scheme: https
          tls_config:
            ca_file: /var/run/secrets/kubernetes.io/serviceaccount/ca.crt
          bearer_token_file: /var/run/secrets/kubernetes.io/serviceaccount/token
          kubernetes_sd_configs:
            - role: node
          relabel_configs:
            - action: labelmap
              regex: __meta_kubernetes_node_label_(.+)
            - target_label: __address__
              replacement: kubernetes.default.svc:443
            - source_labels: [__meta_kubernetes_node_name]
              regex: (.+)
              target_label: __metrics_path__
              replacement: /api/v1/nodes/$1/proxy/metrics/cadvisor
        - job_name: 'kubernetes-service-endpoints'
          honor_labels: true
          kubernetes_sd_configs:
            - role: endpoints
          relabel_configs:
            - source_labels: [__meta_kubernetes_service_annotation_prometheus_io_scrape]
              action: keep
              regex: true
            - source_labels: [__meta_kubernetes_service_annotation_prometheus_io_scrape_slow]
              action: drop
              regex: true
            - source_labels: [__meta_kubernetes_service_annotation_prometheus_io_scheme]
              action: replace
              target_label: __scheme__
              regex: (https?)
            - source_labels: [__meta_kubernetes_service_annotation_prometheus_io_path]
              action: replace
              target_label: __metrics_path__
              regex: (.+)
            - source_labels: [__address__, __meta_kubernetes_service_annotation_prometheus_io_port]
              action: replace
              target_label: __address__
              regex: (.+?)(?::\d+)?;(\d+)
              replacement: $1:$2
            - action: labelmap
              regex: __meta_kubernetes_service_annotation_prometheus_io_param_(.+)
              replacement: __param_$1
            - action: labelmap
              regex: __meta_kubernetes_service_label_(.+)
            - source_labels: [__meta_kubernetes_namespace]
              action: replace
              target_label: namespace
            - source_labels: [__meta_kubernetes_service_name]
              action: keep
              regex: kubex.*
            - source_labels: [__meta_kubernetes_service_name]
              action: replace
              target_label: service
            - source_labels: [__meta_kubernetes_pod_node_name]
              action: replace
              target_label: node
        - job_name: 'kubernetes-service-endpoints-slow'
          honor_labels: true
          scrape_interval: 5m
          scrape_timeout: 30s
          kubernetes_sd_configs:
            - role: endpoints
        # ================================
        # Adding for Nvidia support
        # ================================            
        - job_name: gpu-metrics
          scrape_interval: 1s
          metrics_path: /metrics
          scheme: http
          kubernetes_sd_configs:
          - role: endpoints
            namespaces:
              names:
              - gpu-operator
        # ================================                   
          relabel_configs:
            - source_labels: [__meta_kubernetes_service_annotation_prometheus_io_scrape_slow]
              action: keep
              regex: true
            - source_labels: [__meta_kubernetes_service_annotation_prometheus_io_scheme]
              action: replace
              target_label: __scheme__
              regex: (https?)
            - source_labels: [__meta_kubernetes_service_annotation_prometheus_io_path]
              action: replace
              target_label: __metrics_path__
              regex: (.+)
            - source_labels: [__address__, __meta_kubernetes_service_annotation_prometheus_io_port]
              action: replace
              target_label: __address__
              regex: (.+?)(?::\d+)?;(\d+)
              replacement: $1:$2
            - action: labelmap
              regex: __meta_kubernetes_service_annotation_prometheus_io_param_(.+)
              replacement: __param_$1
            - action: labelmap
              regex: __meta_kubernetes_service_label_(.+)
            - source_labels: [__meta_kubernetes_namespace]
              action: replace
              target_label: namespace
            - source_labels: [__meta_kubernetes_service_name]
              action: keep
              regex: kubex.*
            - source_labels: [__meta_kubernetes_service_name]
              action: replace
              target_label: service
            - source_labels: [__meta_kubernetes_pod_node_name]
              action: replace
              target_label: node
            # ================================
            # Adding for Nvidia support
            # ================================
            - source_labels: [__meta_kubernetes_endpoints_name]
              action: drop
              regex: .*-node-feature-discovery-master
            - source_labels: [__meta_kubernetes_pod_node_name]
              action: replace
              target_label: kubernetes_node
            # ================================  
```

## Verify metrics are being scraped in Prometheus

Query:

`DCGM_FI_DEV_GPU_UTIL`

![[Pasted image 20250521181611.png]]