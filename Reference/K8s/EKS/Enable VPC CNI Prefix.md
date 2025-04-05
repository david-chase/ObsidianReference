#vpc #prefix #eks #karpenter #k8s #chatgpt
# Enabling VPC CNI Prefix Assignment Mode on EKS with Karpenter

## 1. Update the Amazon VPC CNI Plugin to Enable Prefix Assignment

Enable the prefix delegation mode:

```bash
kubectl set env daemonset/aws-node -n kube-system ENABLE_PREFIX_DELEGATION=true
```

Confirm the setting:

```bash
kubectl get ds aws-node -n kube-system -o jsonpath='{.spec.template.spec.containers[0].env[?(@.name=="ENABLE_PREFIX_DELEGATION")].value}'
```

## 2. Configure Karpenter for Prefix Assignment Mode

### Example `NodePool`:

```yaml
apiVersion: karpenter.sh/v1beta1
kind: NodePool
metadata:
  name: prefix-assignment-pool
spec:
  template:
    spec:
      providerRef:
        name: default
      requirements:
        - key: "karpenter.k8s.aws/instance-category"
          operator: "In"
          values: ["c", "m", "r"]
        - key: "karpenter.k8s.aws/instance-size"
          operator: "In"
          values: ["large", "xlarge"]
      kubelet:
        maxPods: 110
      tags:
        "kubernetes.io/cluster/<your-cluster-name>": "owned"
        "karpenter.sh/provisioner-name": "prefix-assignment"
```

## 3. Ensure Security Group and IAM Role Permissions

Attach the following permissions to the IAM role used by EKS nodes:

```json
{
    "Effect": "Allow",
    "Action": [
        "ec2:AssignPrivateIpAddresses",
        "ec2:UnassignPrivateIpAddresses",
        "ec2:DescribeNetworkInterfaces",
        "ec2:CreateNetworkInterface",
        "ec2:AttachNetworkInterface",
        "ec2:DescribeInstances",
        "ec2:DescribeInstanceTypes",
        "ec2:DescribeSubnets"
    ],
    "Resource": "*"
}
```

## 4. Monitor and Validate

Check logs for prefix assignment:

```bash
kubectl logs -n kube-system -l k8s-app=aws-node
```

## 5. Test Node Provisioning with Karpenter

Deploy a sample workload:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: prefix-assignment-test
  namespace: default
spec:
  replicas: 100
  selector:
    matchLabels:
      app: test
  template:
    metadata:
      labels:
        app: test
    spec:
      containers:
      - name: busybox
        image: busybox
        command: ["sh", "-c", "sleep 3600"]
```

Check IP utilization:

```bash
kubectl describe nodes | grep PodCIDR
```

---

With these steps, prefix assignment mode should be successfully enabled on your EKS cluster with Karpenter.
