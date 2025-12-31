#k8s #troubleshooting 

```table-of-contents
```

## Checking for stuck finalizers

``` powershell
# Check for stuck finalizers on an object
# Example: kubectl get application gpu-operator -n argocd -o jsonpath="{.metadata.finalizers}"
kubectl get <kind> <name> -n <namespace> -o jsonpath="{.metadata.finalizers}"
```

## Deleting finalizers

``` powershell
# Remove stuck finalizers on an object
kubectl patch <object> -n <namespace> -p '{"metadata":{"finalizers":[]}}' --type=merge
```

## Test external connectivity

``` powershell
# Check cluster for external connectivity and DNS resolution
kubectl run test-pod --rm -it --image=busybox -- /bin/sh
ping google.com
```

## Open a shell on a container

``` powershell
kubectl exec -it <podname> -c <containername> -- /bin/sh
```

