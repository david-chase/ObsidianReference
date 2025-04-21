#k8s #linux #troubleshooting #logs

if `kubectl` stops responding (possibly due to API server issues), you may need to log directly into your Kubernetes nodes (master or worker) and check the logs stored on the filesystem.

This helps you diagnose issues when the usual `kubectl` commands aren’t available.

**On systems using** `**systemd**`**:**

Use `journalctl`:

`journalctl --unit kubelet # For kubelet logs on worker nodes`

`journalctl --unit kube-apiserver # For API server logs on master nodes journalctl --unit kube-scheduler # For scheduler logs on master nodes journalctl --unit kube-controller-manager # For controller manager logs on master nodes`

`journalctl --unit kube-proxy # For kube-proxy logs on worker nodes`