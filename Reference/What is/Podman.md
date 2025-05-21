#containers #oci #product #dev

https://podman.io/

**Podman** is an **open-source container engine** that allows you to **build, run, manage, and share containers and container images**. It’s often considered a **drop-in replacement for Docker**, but with some architectural differences that make it appealing for certain use cases, especially in **enterprise and security-sensitive environments**.

Developed by **Red Hat**, Podman is widely used in **Linux distributions** and **cloud-native environments**.

---

## 🔹 What is Podman?

- A **daemonless container engine** (no background service required).
    
- Provides **Docker-compatible CLI commands** (`podman run`, `podman build`, etc.).
    
- Supports **rootless containers** (runs containers as a non-root user for better security).
    
- Can manage **pods**, similar to Kubernetes pods.
    
- Designed to integrate with **systemd** and **OCI standards**.
    

---

## 🔹 Key Features

|Feature|Description|
|---|---|
|**Daemonless Architecture**|Runs containers directly without a long-running daemon.|
|**Rootless Containers**|Can run containers without needing root privileges.|
|**Docker CLI Compatibility**|Compatible with Docker commands (`alias docker=podman`).|
|**Pod Management**|Supports running groups of containers in "pods" (like Kubernetes pods).|
|**Systemd Integration**|Can generate systemd unit files to manage containers as services.|
|**Buildah & Skopeo Integration**|Works with Buildah for building images and Skopeo for image management.|
|**Open Container Initiative (OCI) Compliant**|Uses industry-standard image formats and runtimes.|