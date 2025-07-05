#mig #sharing #gpu #nvidia #k8s

### 🔀 Side-by-Side Comparison

|Feature|**MIG (Multi-Instance GPU)**|**GPU Time Slicing**|
|---|---|---|
|**Type of sharing**|**Hardware-based** partitioning|**Software-based** time multiplexing|
|**Isolation**|Strong (dedicated cores, memory, cache)|Weak (processes take turns on shared hardware)|
|**Performance**|Predictable, consistent|Variable (depends on scheduling fairness and load)|
|**Available on**|A100, H100, L40S, some newer GPUs|Most NVIDIA GPUs|
|**Number of workloads**|Limited by MIG profiles (e.g., 7 per A100 max)|Higher concurrency possible, but with contention|
|**K8s Support**|Built into GPU Operator and device plugin (`nvidia.com/mig-<profile>`)|Needs manual setup with NVIDIA time-slicing toolkit|
|**Latency-sensitive workloads**|✅ Suitable|⚠️ Often not ideal|
|**Deployment use case**|Production workloads needing isolation|Dev/test, bursty jobs, lightweight inference|