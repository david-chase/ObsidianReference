#product #deployment #k8s

https://flagger.app/

**Flagger** is an **open-source progressive delivery tool for Kubernetes**. It automates **canary deployments, blue/green deployments, and A/B testing** for applications running in Kubernetes clusters.

Flagger works by continuously **monitoring metrics and health checks** of new versions of applications and gradually shifting traffic to them. If something goes wrong, it can **automatically rollback** to the previous version.

It’s commonly used with service meshes (like Istio, Linkerd, Kuma) or ingress controllers (NGINX, Contour, Traefik) to **control traffic routing during deployments**.

---

## 🔹 What is Flagger?

- A **Kubernetes operator** that automates **progressive delivery**.
- Helps deploy new versions of applications **safely and incrementally**.
- Reduces deployment risks by analyzing metrics before shifting traffic.
- Integrates with **Prometheus, Datadog, CloudWatch**, and others for monitoring.
- Works with **GitOps workflows (Flux, Argo CD)**.

---

## 🔹 Key Features of Flagger

|Feature|Description|
|---|---|
|**Canary Deployments**|Gradually shifts traffic to new versions while monitoring health.|
|**Blue/Green Deployments**|Swaps versions after verification tests.|
|**A/B Testing**|Routes traffic based on headers, cookies, or weight.|
|**Automated Rollbacks**|Reverts to a previous version if the new version fails health checks.|
|**Metric Analysis**|Uses Prometheus (or others) to analyze success rates, latency, error ratios.|
|**Multi-Platform Support**|Supports Istio, Linkerd, Contour, NGINX, Traefik, App Mesh, Gateway API.|
|**Webhooks for Custom Checks**|Supports manual approval steps or custom validation webhooks.|