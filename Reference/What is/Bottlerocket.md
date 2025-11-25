#aws #linux #eks

https://bottlerocket.dev/

Bottlerocket is an open-source, Linux-based operating system from AWS that is purpose-built for running containers on virtual machines or bare metal. It focuses on security, immutability, and simplified operations. Unlike general-purpose Linux distributions, Bottlerocket uses an image-based update model, minimizes the userspace, and integrates tightly with orchestrators like Kubernetes and ECS.

## Key Characteristics

### Design Goals

- Minimal host OS surface area to reduce vulnerabilities
- Strong security defaults (SELinux, dm-verity, API-only admin access)
- Image-based updates for predictable rollouts and easy rollbacks
- Consistent behavior across EC2 and on-premise environments

### Architecture

- Root filesystem is immutable and updated as a whole image
- User data and configuration reside on a separate partition
- Containers are split into:
    - Host containers for admin and control operations
    - Service containers for system responsibilities
    - Workload containers for your applications

### Kubernetes Integration

- Official EKS-optimized Bottlerocket images
- Support for:
    - Cluster API
    - SSM Session Manager for admin access
    - Containerd runtime
    - Node provisioning via Launch Templates
- Designed to reduce drift and harden node security in Kubernetes clusters

### Benefits

- Lower operational overhead compared to general Linux distributions
- Fewer CVEs due to smaller package footprint
- Consistent, atomic upgrades with automatic rollback
- Better alignment with container-focused workflows

### Use Cases

- EKS managed node groups and self-managed node groups
- ECS clusters
- Large fleets where security, repeatability, and minimal host variation are priorities
- Regulated environments where immutable infrastructure is desirable

