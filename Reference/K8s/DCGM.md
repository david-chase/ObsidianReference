#dgcm #prometheus #metrics #nvidia #k8s 

## See also

[[Install NVIDIA GPU drivers on Ubuntu]]

## Deploying the NVIDIA Operator

https://docs.nvidia.com/datacenter/cloud-native/gpu-operator/latest/getting-started.html

## Setting up Prometheus

https://docs.nvidia.com/datacenter/cloud-native/gpu-telemetry/latest/kube-prometheus.html

#### Modifying the AIO chart values.yaml

``` yaml
      scrape_configs:
[...]
        # ================================
        # Adding for Nvidia support
        # ================================            
        - job_name: gpu-metrics
          scrape_interval: 10s
          metrics_path: /metrics
          scheme: http
          kubernetes_sd_configs:
          - role: endpoints
            namespaces:
              names:
              - gpu-operator
          relabel_configs:
            - source_labels: [__meta_kubernetes_endpoints_name]
              action: drop
              regex: .*-node-feature-discovery-master
            - source_labels: [__meta_kubernetes_pod_node_name]
              action: replace
              target_label: kubernetes_node
#            - source_labels: [__meta_kubernetes_service_name]
#              action: keep
#              regex: nvidia-dcgm-exporter|gpu-operator   
```

## Verify metrics are being scraped in Prometheus

Query:

`DCGM_FI_DEV_GPU_UTIL`

![[Pasted image 20250521181611.png]]