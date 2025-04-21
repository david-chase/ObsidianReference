
# Docker Nodes Local Storage

## Overview

When running Kubernetes nodes using Docker (e.g., via Minikube), local storage for each node is managed by Docker and resides on the **host machine**, not within the Docker image itself.

---

## Key Concepts

- **Docker Image Layers**: Immutable; do not store persistent data.
- **Writable Layer**: A per-container writable filesystem stored on the host.
- **Volumes**: Docker volumes used by containers are stored on the host.
- **Kubelet Pod Storage**: Managed inside the container, but stored on host.
- **Container Logs**: Stored in Docker’s internal container data directories.

---

## Default Docker Storage Paths on Ubuntu

| What                        | Host Path Example                                  | Notes |
|-----------------------------|-----------------------------------------------------|-------|
| Writable Layer              | `/var/lib/docker/overlay2/`                        | Specific path found via `docker inspect` |
| Volumes                     | `/var/lib/docker/volumes/`                         | Holds mounted data, including pod volumes |
| Kubelet Pod Data            | Depends on container mounts (`/var/lib/kubelet`)   | May be in overlay2 or host mount |
| Container Logs              | `/var/lib/docker/containers/<container-id>/`       | Logs per container |

---

## Helpful Commands

- Get writable layer path:
  ```bash
  docker inspect node01 --format '{{ .GraphDriver.Data.UpperDir }}'
  ```

- Get volume mounts:
  ```bash
  docker inspect node01 --format '{{ json .Mounts }}' | jq
  ```

- Find kubelet data source path:
  ```bash
  docker inspect node01 --format '{{ range .Mounts }}{{ if eq .Destination "/var/lib/kubelet" }}{{ .Source }}{{ end }}{{ end }}'
  ```

- Find container log path:
  ```bash
  docker inspect node01 --format '{{ .LogPath }}'
  ```

---

## Summary

Even when nodes run in Docker containers, all their writable data, volume mounts, and logs are stored **on the host machine**, typically under `/var/lib/docker/`.

