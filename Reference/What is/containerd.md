#cri #k8s #product #runtime

https://containerd.io/

**containerd** is an **industry-standard container runtime** that manages the complete container lifecycle on a host system. It's a core component of the container ecosystem, used by Kubernetes, Docker, and other platforms to run containers.

---

## 🔹 What containerd Does:

1. **Container Lifecycle Management**:
    
    - Pulls container images.
        
    - Creates, starts, stops, and deletes containers.
        
    - Manages container storage and networking.
        
2. **Image Management**:
    
    - Handles downloading, caching, and storing of container images from registries.
        
3. **Low-Level Runtime**:
    
    - containerd itself interacts with lower-level runtimes like **runc** to create and manage Linux containers.
        
4. **Namespace Support**:
    
    - Provides isolation using Linux namespaces and cgroups (control groups).