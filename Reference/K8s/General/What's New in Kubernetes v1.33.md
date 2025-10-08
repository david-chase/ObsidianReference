#k8s #releases #whatsnew

Kubernetes 1.33 includes **64 enhancements** in total:

- 18 features graduated to **Stable / GA** [Kubernetes+1](https://kubernetes.io/blog/2025/04/23/kubernetes-v1-33-release/?utm_source=chatgpt.com)
- 20 entering **Beta** status [Kubernetes+2vCluster+2](https://kubernetes.io/blog/2025/04/23/kubernetes-v1-33-release/?utm_source=chatgpt.com)
- 24 in **Alpha** [Kubernetes+1](https://kubernetes.io/blog/2025/04/23/kubernetes-v1-33-release/?utm_source=chatgpt.com)
- 2 features deprecated or removed [Kubernetes](https://kubernetes.io/blog/2025/04/23/kubernetes-v1-33-release/?utm_source=chatgpt.com)

Here are the key changes:

|Area|Feature / Change|Description / Impact|
|---|---|---|
|**Native Sidecars (Stable / GA)**|Sidecar containers are now a first-class concept|You can mark containers as sidecars; Kubernetes ensures they start before the main containers, run concurrently, and shut down after — with proper probe support and OOM behavior. [Kloia+3Kubernetes+3vCluster+3](https://kubernetes.io/blog/2025/04/23/kubernetes-v1-33-release/?utm_source=chatgpt.com)|
|**In-place Pod Vertical Scaling (Beta)**|Change CPU / memory on a running Pod without deletion|This reduces downtime when resizing resources. [Kubernetes+3Sysdig+3vCluster+3](https://www.sysdig.com/blog/kubernetes-1-33-whats-new?utm_source=chatgpt.com)|
|**User Namespaces in Pods (Beta, on by default)**|Better isolation of container UIDs|Containers can run “as root” internally but are mapped to non-root UIDs on the node, limiting host privileges. [Sysdig+3Kubernetes+3vCluster+3](https://kubernetes.io/blog/2025/03/26/kubernetes-v1-33-upcoming-changes/?utm_source=chatgpt.com)|
|**ServiceAccount Token Enhancements**|Stronger token binding, more metadata|Tokens can carry a unique token ID (JTI) and node identity, improving validation, auditing, and reducing replay risk. [Kubernetes+4Medium+4vCluster+4](https://medium.com/google-cloud/upgrades-everything-new-with-kubernetes-1-33-2daead6b9fb4?utm_source=chatgpt.com)|
|**Extended Loopback Client Certificate Validity**|14 months lifetime for internal API server cert|Reduces certificate renewal overhead aligning with Kubernetes support windows. [Sysdig+1](https://www.sysdig.com/blog/kubernetes-1-33-whats-new?utm_source=chatgpt.com)|
|**IP / CIDR Format Warnings**|More strict validation and warnings|Non-standard formats (e.g. padded zeroes) now emit warnings to encourage good practices. [Sysdig+1](https://www.sysdig.com/blog/kubernetes-1-33-whats-new?utm_source=chatgpt.com)|
|**Removal / Deprecation of Fields & APIs**|- Removal of `status.nodeInfo.kubeProxyVersion`  <br>- Deprecation of Endpoints API  <br>- Removal of `gitRepo` volume type  <br>- Disallow `hostNetwork: true` on Windows Pods|See migration paths, especially for tooling depending on these APIs. [vCluster+3Kubernetes+3Kloia+3](https://kubernetes.io/blog/2025/03/26/kubernetes-v1-33-upcoming-changes/?utm_source=chatgpt.com)|
|**Other Notables**|<ul><li>Support for multiple Service CIDRs and better IPAM APIs (ServiceCIDR, IPAddress) [Medium+2vCluster+2](https://medium.com/google-cloud/upgrades-everything-new-with-kubernetes-1-33-2daead6b9fb4?utm_source=chatgpt.com)</li><li>“Ordered Namespace Deletion” (Alpha) — deterministic cleanup of resources in a namespace [Kubernetes+1](https://kubernetes.io/blog/2025/03/26/kubernetes-v1-33-upcoming-changes/?utm_source=chatgpt.com)</li><li>Pod generation tracking (Alpha) adds a `generation` metadata field for pods to reflect spec changes [Sysdig+1](https://www.sysdig.com/blog/kubernetes-1-33-whats-new?utm_source=chatgpt.com)</li><li>OCI image / artifact volumes (Alpha) — mount images/artifacts as read-only volumes inside Pods [Sysdig+2vCluster+2](https://www.sysdig.com/blog/kubernetes-1-33-whats-new?utm_source=chatgpt.com)</li><li>kubectl `.kuberc` (Alpha) — separate configuration for `kubectl` aliases, defaults, etc. [Kloia+2vCluster+2](https://www.kloia.com/blog/kubernetes-1.33-what-is-new?utm_source=chatgpt.com)</li></ul>||

---

## ⚠️ Deprecations & Breaking Changes

- The `status.nodeInfo.kubeProxyVersion` field will be **removed**. [Kubernetes+1](https://kubernetes.io/blog/2025/03/26/kubernetes-v1-33-upcoming-changes/?utm_source=chatgpt.com)
- The **Endpoints API** is deprecated; you should migrate clients to **EndpointSlices**. [Kloia+2Kubernetes+2](https://www.kloia.com/blog/kubernetes-1.33-what-is-new?utm_source=chatgpt.com)
- The **gitRepo volume type** is removed (it was insecure). [Kloia+2Kubernetes+2](https://www.kloia.com/blog/kubernetes-1.33-what-is-new?utm_source=chatgpt.com)
- On **Windows Pods**, `hostNetwork: true` is withdrawn / not allowed. [Kloia+2Kubernetes+2](https://www.kloia.com/blog/kubernetes-1.33-what-is-new?utm_source=chatgpt.com)

---

## 🛠 What This Means for Cluster Operators & Users

- **Upgrade planning matters.** Some features you rely on may now be deprecated; test early.
- **Feature gates & defaults.** Some of the newly graduated features (sidecars, in-place scaling) may require enabling or confirming defaults in your cluster configuration.
- **Security improvements.** Stronger token binding and user namespace defaults help reduce blast radius if containers are compromised.
- **Better resource management.** In-place vertical scaling unlocks new possibilities for autoscaling without disruption.
- **Cleaner configurations.** With warnings for IP/CIDR formats and API deprecations, there’s more safety checking to prevent misconfigurations.