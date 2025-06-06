#dgcm #prometheus #metrics #nvidia #k8s 

## See also

[[Install NVIDIA GPU drivers on Ubuntu]]

## 1. Install CUDA toolkit or NVIDIA GPU drivers

[Install NVIDIA GPU drivers on Ubuntu](https://chatgpt.com/share/682f24e5-e03c-8003-a947-7b53cd1d93da)

## 2. Deploy the NVIDIA Operator or DCGM Exporter

https://docs.nvidia.com/datacenter/cloud-native/gpu-operator/latest/getting-started.html
https://docs.nvidia.com/datacenter/cloud-native/gpu-telemetry/latest/kube-prometheus.html

## 3. Redeploy the All-in-One Helm chart with a scraper added for gpu-metrics

#### 3.1 Download the AIO Helm chart locally

``` bash
helm fetch densify/kubex-automation-stack --untar
```

#### 3.2 Modify the AIO chart values.yaml 

Add the following to the end of the `scrape_configs section` in `kubex-automation-stack/values.yaml`:

``` yaml
        # Adding for NVIDIA support          
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
```


#### 3.3 Redeploy the Helm chart from the local repo

``` bash
helm install --create-namespace -n densify -f values-edit.yaml kubex .\kubex-automation-stack\
```

Or using your gitops tool of choice.

## 4. Verify metrics are being scraped in Prometheus

Run the following query in Prometheus and confirm it returns values:

`DCGM_FI_DEV_GPU_UTIL`

![[Pasted image 20250522092839.png]]