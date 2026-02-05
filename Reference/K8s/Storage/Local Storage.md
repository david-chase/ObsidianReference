#storage #k8s #local

```table-of-contents
```
## EmptyDir

An **EmptyDir** is a volume type that is created when a pod is assigned to a node. It exists as long as that pod is running on that node. It is physically stored on the node’s disk or in RAM (if using `medium: Memory`). When the pod is deleted or evicted, the data in the **EmptyDir** is permanently deleted.

---
## Ephemeral Storage

**Ephemeral storage** is a broader category in Kubernetes that refers to any storage that does not persist across pod lifetimes. This includes:

- EmptyDir: Volumes for scratch space or temporary caches.
- Container Writable Layer: The local disk space used by a container to store logs and any files written to the root filesystem.
- ConfigMaps and Secrets: Injected into the pod as volumes.
- Generic Ephemeral Volumes: Ephemeral volumes that support the same features as persistent volumes (like snapshots or resizing) but are still deleted when the pod is deleted.