#argocd #limits #requests #deployment #mac

To ignore changes in Requests & Limits for a single app, add the following to the Argo CD application manifest:

``` yaml
spec:
  ignoreDifferences:
    - group: "*"
      kind: "*"
      jqPathExpressions:
        - '.spec.template.spec.containers[].resources.requests'
        - '.spec.template.spec.containers[].resources.limits'
```

To ignore it globally add this to the `argocd-cm` ConfigMap:

``` yaml
data:
  resource.customizations: |
    apps/Deployment:
      ignoreDifferences: |
        jqPathExpressions:
          - .spec.template.spec.containers[].resources.requests
          - .spec.template.spec.containers[].resources.limits
    apps/StatefulSet:
      ignoreDifferences: |
        jqPathExpressions:
          - .spec.template.spec.containers[].resources.requests
          - .spec.template.spec.containers[].resources.limits
    apps/DaemonSet:
      ignoreDifferences: |
        jqPathExpressions:
          - .spec.template.spec.containers[].resources.requests
          - .spec.template.spec.containers[].resources.limits
```