#gpu #ai #nvidia #nodes #k8s #labels

## ✅ Standard Node Labels for GPU Nodes

|Label Key|Description|Example|
|---|---|---|
|`nvidia.com/gpu.present`|Automatically set to `"true"` by the [NVIDIA device plugin](https://github.com/NVIDIA/k8s-device-plugin) when a GPU is detected|`nvidia.com/gpu.present=true`|
|`nvidia.com/gpu.product`|Indicates the GPU model (used by NVIDIA GPU Operator)|`nvidia.com/gpu.product=Tesla-T4`|
|`nvidia.com/gpu.count`|Number of GPUs on the node (optional custom)|`nvidia.com/gpu.count=1`|