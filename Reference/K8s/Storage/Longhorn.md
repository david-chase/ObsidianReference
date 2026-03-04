```table-of-contents
```

## Longhorn and Storage Pooling

Longhorn does not allow you to pool local disks from multiple nodes to create a single large volume that exceeds the capacity of an individual node.

### Block Storage Architecture

Longhorn is a distributed block storage system, not a distributed file system like Ceph or GlusterFS. Because it functions as a block device provider, the maximum size of any single volume is limited by the largest available disk space on a single node within the cluster.

### How Longhorn Uses Node Storage

While Longhorn manages the storage across your nodes (Havana, Miami, and Obsidian) as a collective pool for scheduling purposes, the data placement follows these rules:

- **Replica Limitation**: Each volume consists of an engine and multiple replicas. Each replica must fit entirely on a single node.
- **No Striping Across Nodes**: Longhorn does not stripe a single replica across multiple nodes to increase volume size. It uses the distributed nature of the cluster for redundancy (replication) rather than for spanning a single volume across the combined capacity of the cluster nodes.
- **Provisioning Capacity**: If Havana has 500GB of storage and Miami has 500GB, you still cannot create a 1TB volume. You can, however, create multiple 200GB volumes until the total capacity of the nodes is reached, depending on your replication factor.

### ReadWriteMany Support

Although Longhorn is natively block storage, it provides **ReadWriteMany (RWX)** support by automatically spinning up an ephemeral NFS server (the Share Manager pod) on top of a Longhorn block volume. Even in this configuration, the underlying storage for that NFS share is still a standard Longhorn volume, which remains bound by the size limits of a single node's disk.