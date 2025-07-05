#gpu #k8s #nvidia #sharing 


| Sharing strategy                                   | Signature                                                                     | Type                 | Comments                                                                                                              |
| -------------------------------------------------- | ----------------------------------------------------------------------------- | -------------------- | --------------------------------------------------------------------------------------------------------------------- |
| MPS                                                | `nvidia-cuda-mps-control`                                                     | daemonset            | Shares GPU context between multiple CUDA processes                                                                    |
| MPS                                                | `kubectl describe pod <pod-name> \| Select-String "CUDA_MPS"`                 | environment variable |                                                                                                                       |
| MIG                                                | `nvidia-mig-manager`                                                          | daemonset            |                                                                                                                       |
| NVIDIA GPU Time Slicing (Default Driver Scheduler) | n/a                                                                           |                      | If no other sharing tech is being used and multiple pods are sharing GPUs, you're probably using default time slicing |
| `nvidia-time-slicing` Toolkit                      | `kubectl get pod <pod-name> -o yaml \| Select-String "NVIDIA_TIME_SLICE"`<br> | environment variable |                                                                                                                       |
