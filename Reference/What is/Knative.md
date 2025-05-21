#k8s #dev #product #serverless

https://knative.dev/docs/

**Knative** is an open-source Kubernetes-based platform that simplifies deploying and managing modern, serverless workloads. It extends Kubernetes with components that make it easier to build, deploy, and scale cloud-native applications, especially those following event-driven and microservices patterns.

---

## 🔧 **Key Components of Knative**

1. **Knative Serving**
    
    - Manages stateless, request-driven services.
        
    - Automatically scales workloads up (even to zero) and down based on demand.
        
    - Provides traffic management (blue/green deployments, canary releases, traffic splitting).
        
    - Simplifies deploying and exposing containerized applications as services.
        
2. **Knative Eventing**
    
    - Provides a consistent model for consuming and producing events.
        
    - Connects event sources (like Kafka, Google Pub/Sub, etc.) to Kubernetes services.
        
    - Helps build event-driven architectures with decoupled microservices.
        
3. **Knative Functions (optional)**
    
    - Simplifies writing and deploying small functions as serverless workloads.
        
    - Supports Function-as-a-Service (FaaS) patterns, integrating with frameworks like OpenFunction.