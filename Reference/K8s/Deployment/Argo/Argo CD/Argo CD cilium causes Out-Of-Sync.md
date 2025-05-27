#argocd #deployment #k8s #cilium 

Add this to the bottom of your `argocd-cm` ConfigMap:

``` yaml
data:
  resource.exclusions: |
    - apiGroups:
        - cilium.io
      kinds:
        - CiliumIdentity
```

Then restart the deployment "`argocd-applicationset-controller`".