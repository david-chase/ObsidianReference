#operator #gpu #nvidia #k8s 

### 🧩 NVIDIA GPU Operator Components

|**Component**|**Purpose**|
|---|---|
|**nvidia-driver-daemonset**|Installs and manages NVIDIA GPU kernel drivers on each node.|
|**nvidia-container-toolkit**|Enables Docker/container runtimes to access GPU resources.|
|**nvidia-device-plugin**|Discovers and registers GPUs (or MIGs) with Kubernetes as schedulable resources.|
|**nvidia-dcgm**|Installs the **Data Center GPU Manager**—used for health monitoring, telemetry, and diagnostics.|
|**nvidia-dcgm-exporter**|Exposes GPU health and usage metrics (from DCGM) in Prometheus format.|
|**nvidia-container-runtime**|GPU-aware container runtime that enables launching containers with GPU access.|
|**nvidia-fabricmanager**|Required for multi-GPU systems (like NVLink/NVSwitch); manages GPU interconnects.|
|**nvidia-mig-manager** (optional)|Handles automatic configuration and lifecycle of MIG devices if MIG mode is enabled.|
|**node-feature-discovery (NFD)**|Labels nodes based on hardware features, including GPU availability and MIG capabilities.|
|**validation webhook** (optional)|Ensures pods requesting GPU resources are valid; improves scheduling reliability.|

