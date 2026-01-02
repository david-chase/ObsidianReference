#k8s #crd #controller 

```table-of-contents
```
## Scopes

- Custom Resource Definitions (CRDs) are always cluster scoped.
- Individual CRs can be cluster or namespace-scoped depending on how they're defined.

``` yaml
spec:
  scope: Namespaced   # or Cluster
```

## Deleting a CR

- To delete a CR you reference the resource type, the resource, and the namespace it's in.  

``` powershell
# Delete a CR that is an Argo CD application
kubectl delete application <name> -n argocd

# Delete a CR that is a cluster-scoped cert-manager clusterissuer
kubectl delete clusterissuer <name>
```

## Deleting a CRD

``` powershell
kubectl delete crd <name>
```

- When you delete a CRD, the API queues up all resources of that type and deletes them as well (https://marcincuber.medium.com/what-happens-when-you-delete-a-kubernetes-customresourcedefinition-crd-d5e741fb6441)

## What is a CRD?

- A **CustomResourceDefinition (CRD)** is a Kubernetes API object that lets you define your own resource types (e.g., `Backup`, `ClusterPolicy`, etc.).
- It’s a way to **extend Kubernetes** with new kinds of objects, behaving like built-in ones.

## Is a CRD just metadata?

- **Yes.** A CRD is mostly **metadata and schema** — it registers a new resource type and defines its structure.
- It **does not include behavior** on its own.

## What gives a CRD functionality?

- A CRD is **usually paired with a controller** (often as part of an Operator).
- The controller **watches for events** (create, update, delete) on the custom resource and **takes actions** accordingly.

## Analogy

|Component|Role|
|---|---|
|CRD|Defines _what_ the resource is|
|Controller|Defines _what to do_ with it|
