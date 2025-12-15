#product #networking #project #k8s 

https://kgateway.dev/

KGateway is a Kubernetes-native API gateway and ingress platform built on top of Envoy, designed to provide advanced traffic management, security, and observability for Kubernetes workloads. It is positioned as a modern, high-performance gateway that integrates tightly with Kubernetes APIs and service-mesh-adjacent patterns without requiring a full mesh.

## What KGateway is

- A Kubernetes API gateway / ingress controller
- Built on Envoy Proxy as the data plane
- Uses Kubernetes CRDs to define routing, policies, and security
- Designed for north-south (ingress) traffic, not service-mesh sidecars
- Successor and evolution of technology originating from Gloo Gateway (Solo.io)

## Core capabilities

- HTTP, HTTPS, gRPC, and TCP routing
- Advanced L7 routing (headers, paths, methods, weights)
- TLS termination and mTLS support
- Authentication and authorization (OIDC, JWT, external auth)
- Rate limiting and request shaping
- Traffic splitting and canary deployments
- WebSocket support
- Native Kubernetes integration (Ingress, Gateway API, CRDs)

## Kubernetes integration model

- Runs as a centralized gateway deployment (not sidecars)
- Controlled via:
    - Kubernetes Gateway API (preferred, standards-based)
    - Custom Resource Definitions (for advanced features)
- Uses standard Kubernetes Service, EndpointSlice, and Secret objects
- Works with existing CNI and does not require a service mesh

## Gateway API support

- First-class support for Kubernetes Gateway API
- Supports:
    - Gateway
    - HTTPRoute
    - TLSRoute
    - TCPRoute
- Adds extended capabilities beyond the base Gateway API via CRDs

## Data plane and control plane

- Data plane: Envoy proxies
- Control plane:
    - Watches Kubernetes resources
    - Translates routing and policy into Envoy configuration
    - Pushes updates dynamically without restarts

## Security features

- TLS and mTLS
- JWT validation
- OIDC authentication
- External authorization integrations
- Fine-grained RBAC-style policies at the gateway layer

## Observability

- Prometheus metrics
- Access logs
- Distributed tracing integration (e.g., OpenTelemetry)
- Envoy-native stats exposed through Kubernetes-friendly endpoints

## Typical use cases

- API gateway in front of Kubernetes services
- Replacement for legacy ingress controllers with richer L7 control
- Gateway API-based ingress standardization
- Multi-cluster or multi-environment ingress consistency
- Fine-grained traffic control without deploying a service mesh