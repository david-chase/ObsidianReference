#cni #k8s #networking #openshift 

[https://ovn-kubernetes.io/](https://ovn-kubernetes.io/)

OVN-Kubernetes is an open source networking solution for Kubernetes clusters that leverages Open Virtual Network (OVN) and Open vSwitch (OVS) to provide a programmable software-defined networking (SDN) platform. It functions as a Container Network Interface (CNI) plugin, translating high-level Kubernetes objects like Pods, Services, and NetworkPolicies into OVN logical entities. These entities are then converted into OpenFlow rules to manage packet forwarding at the node level. Originally developed to bring mature datacenter-grade networking to container orchestration, it is now the default networking provider for Red Hat OpenShift and a CNCF Sandbox project.

## Core Networking Capabilities

- Kubernetes CNI Conformance: Provides essential networking including Pod IP Address Management (IPAM) and virtual ethernet interface creation.
- Overlay Networking: Uses the Generic Network Virtualization Encapsulation (Geneve) protocol to create a secure and flexible overlay network between nodes.
- Service Implementation: Implements Kubernetes Services and EndpointSlices through native OVN Load Balancers rather than relying on kube-proxy.
- Policy Enforcement: Handles Kubernetes NetworkPolicies and AdminNetworkPolicies using OVN Access Control Lists (ACLs).
- Dual-Stack Support: Full support for IPv4, IPv6, and dual-stack cluster configurations.

## Advanced Traffic Control

- Egress IP: Allows administrators to assign specific source IP addresses for traffic exiting the cluster, facilitating integration with external firewalls and security systems.
- Egress Firewall: Provides the ability to restrict egress traffic from cluster workloads to specific external destinations.
- Quality of Service (QoS): Supports Differentiated Services Code Point (DSCP) marking and rate limiting for traffic management.
- Multihoming: Integrates with Multus CNI to allow pods to connect to multiple network interfaces, supporting complex telco and enterprise use cases.

## Architecture and Performance

- Distributed Control Plane: Features an Interconnect mode that distributes OVN databases across nodes to eliminate centralized bottlenecks and improve stability.
- Hardware Offload: Supports OVS Hardware Offload to SmartNICs and DPUs, reducing CPU overhead and providing near-wire-speed performance.
- Scalability: Designed to support large-scale clusters with thousands of nodes by minimizing RAFT coordination overhead in distributed modes.
- Hybrid Networking: Supports mixed Windows and Linux clusters using VXLAN tunnels for cross-platform compatibility.