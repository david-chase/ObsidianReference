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

## Create a debugger container on a pod

- This is useful for containers that are distroless -- they have no shell in them.
- In fact, this is probably preferred to opening a shell on a container since it doesn't rely on anything being included in the container image

``` powershell
# Create an interactive debugging session in pod mypod and immediately attach to it.
kubectl debug mypod -it --image=busybox
```

## Create a debug pod that has all the same mounts (config/data) as the container I'm debugging

``` powershell
kubectl debug <podname> -n <namespace> `
  --copy-to=my-pod-debug `
  --container=<containername> `
  --image=busybox `
  -it
  
# Example for kubex automation controller
kubectl debug kubex-automation-controller-84d56875-4t96h -n kubex `
  --copy-to=my-pod-debug `
  --container=kubex-automation-controller `
  --image=busybox `
  -it
```