#product #project 

https://cdk8s.io/

**cdk8s** (Cloud Development Kit for Kubernetes) is a **software framework for defining Kubernetes resources using general-purpose programming languages** instead of raw YAML.

Instead of hand-writing Kubernetes manifests, you write code (TypeScript, Python, Java, Go, C#) that **synthesizes into standard Kubernetes YAML**, which you then apply with `kubectl`, Argo CD, Flux, or Helm-free workflows.

cdk8s is built and maintained by AWS, but it is **vendor-neutral** and works with any Kubernetes cluster.


## Core ideas

- Infrastructure is defined as **code**, not templates
- Code compiles (“synthesizes”) into **plain Kubernetes manifests**
- Output YAML is cluster-agnostic and GitOps-friendly
- No runtime components inside the cluster

## How cdk8s works

1. You write Kubernetes definitions using a supported language
2. cdk8s uses Kubernetes OpenAPI schemas to generate strongly-typed constructs
3. `cdk8s synth` emits YAML manifests
4. YAML is applied using standard tooling (kubectl, Argo CD, Flux, etc.)

There is **no controller, webhook, or CRD required at runtime**.

## Supported languages

- TypeScript (most mature ecosystem)
- Python
- Java
- Go
- C#

## Key components

- **cdk8s CLI**
    - Project scaffolding
    - Synthesis to YAML
- **cdk8s-core**
    - App, Chart, Construct abstractions
- **cdk8s-plus**
    - Higher-level Kubernetes abstractions (e.g., Deployment, Service, Ingress)
- **Imports**
    - Generate constructs from CRDs or Helm charts

## What cdk8s is good at

- Eliminating copy-paste YAML
- Enforcing consistency via code reuse
- Conditional logic that YAML cannot express cleanly
- Strong typing and IDE autocomplete
- Programmatic generation of large numbers of manifests
    
- GitOps-friendly workflows without Helm
    

## What cdk8s is not

- Not a runtime orchestrator
- Not a replacement for kubectl, Argo CD, or Flux
- Not a Helm renderer (though it can import Helm charts)
- Not a cluster lifecycle tool