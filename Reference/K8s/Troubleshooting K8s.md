#k8s #troubleshooting 

```table-of-contents
```

## Tracing webhook effects

To see which mutating webhooks have acted on a pod and what changes they made, you can use the Kubernetes Audit Logs. In a production-grade cluster like yours (v1.35 on bare metal), the API server can be configured to record these mutations in its audit trail.

### Kubernetes Audit Logs

When a mutating webhook modifies an object, the Kubernetes API server records the invocation and the resulting patch in the audit log, provided the audit policy level is set to **RequestResponse**.

#### Finding the Audit Policy

Since you used **kubeadm**, your API server manifest is located at `/etc/kubernetes/manifests/kube-apiserver.yaml`. You need to check if audit logging is enabled by looking for these flags:

- `--audit-policy-file`
- `--audit-log-path`

If enabled, you can search the log file (usually on the **Havana** node) for your pod's name.

#### Identifying Mutations in the Log

Look for entries where the `annotations` field includes keys starting with:

- `mutation.webhook.admission.k8s.io/round_{round_idx}_index_{order_idx}`: This confirms which webhook was called and in what order.
- `patch.webhook.admission.k8s.io/round_{round_idx}_index_{order_idx}`: This contains the actual JSON patch (base64 encoded) that the webhook applied to your pod.

### Managed Fields

If audit logging is not enabled, you can get a partial history of who "owns" certain fields in the pod's metadata. This is visible in the `managedFields` section of the pod's YAML output.

PowerShell

```
kubectl get pod <pod-name> -o yaml
```

Look for the `managedFields` section. It will list different managers (like `kube-controller-manager`, `argocd`, or specific webhook names if they identify themselves) and the fields they last modified. While this won't show the "before and after" values like an audit log, it identifies which controller or manager is responsible for the current state of the resource requests and limits.

### Active Mutating Webhooks

To see a list of all potential candidates that could be modifying your pods, you can list the active configurations in your cluster:

PowerShell

```
kubectl get mutatingwebhookconfigurations
```

You can then inspect a specific configuration to see which services it calls:

PowerShell

```
kubectl get mutatingwebhookconfiguration <config-name> -o yaml
```

### API Server Dry Run

If you want to test how webhooks behave without actually creating a pod, you can use the `--dry-run=server` flag. By increasing the verbosity of the `kubectl` command, you can sometimes see the admission controller headers in the response.

PowerShell

```
kubectl apply -f <your-manifest>.yaml --dry-run=server -v=8
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