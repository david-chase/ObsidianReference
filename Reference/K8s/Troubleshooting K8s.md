#k8s #troubleshooting 

```table-of-contents
```

## Deleting finalizers

``` powershell
kubectl patch <object> -p '{"metadata":{"finalizers":[]}}' --type=merge
```

## Test external connectivity

``` powershell
kubectl run test-pod --rm -it --image=busybox -- /bin/sh
ping google.com
```

## Open a shell on a container

``` powershell
kubectl exec -it <podname> -c <containername> -- /bin/sh
```

