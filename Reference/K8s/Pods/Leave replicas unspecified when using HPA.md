#chatgpt #k8s #hpa #autoscaling
# Kubernetes and HPA: Should You Omit Replicas?


## Exception

If **HPA is not used**, you must specify `replicas` in the Deployment manifest to ensure Kubernetes knows how many pods to run.
