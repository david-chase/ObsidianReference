#sample #mig #nvidia #gpu #k8s 

``` yaml
  "nvidia.com/gpu.count": "7", # Based on the mig config
  "nvidia.com/gpu.product": "NVIDIA-H100-80GB-HBM3",
  "nvidia.com/gpu.replicas": "1",
  "nvidia.com/gpu.sharing-strategy": "none",
  "nvidia.com/mig.capable": "true",
  "nvidia.com/mig.config": "all-1g.10gb",
  "nvidia.com/mig.config.state": "success",
  "nvidia.com/mig.strategy": "single"
```

### Run the nvidia-smi command in K8s

``` powershell
kubectl exec -it -n gpu-operator ds/nvidia-driver-daemonset -- nvidia-smi -L
```

``` text
GPU 0: NVIDIA H100 80GB HBM3 (UUID: GPU-b4895dbf-9350-2524-a89b-98161ddd9fe4)
  MIG 3g.40gb     Device  0: (UUID: MIG-7089d0f3-293f-58c9-8f8c-5ea666eedbde)
  MIG 2g.20gb     Device  1: (UUID: MIG-56c30729-347f-5dd6-8da0-c3cc59e969e0)
  MIG 1g.10gb     Device  2: (UUID: MIG-9d14fb21-4ae1-546f-a636-011582899c39)
  MIG 1g.10gb     Device  3: (UUID: MIG-0f709664-740c-52b0-ae79-3e4c9ede6d3b)
```

### Check the logs of the nvidia mig manager

``` powershell
kubectl logs -n gpu-operator -l app=nvidia-mig-manager -c nvidia-mig-manager
```