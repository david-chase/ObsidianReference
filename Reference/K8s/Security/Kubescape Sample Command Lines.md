#kubescape #k8s #security #cli 

``` shell
# View Compliance summary report per namespace:
kubectl get workloadconfigurationscansummaries

# View Compliance **summary** report for each workload:
kubectl get workloadconfigurationscansummaries -A

# View Compliance **detailed** report for each workload:
kubectl get workloadconfigurationscans -A

# View Vulnerabilities summary report per namespace:
kubectl get vulnerabilitysummaries

# View vulnerabilities **summary** report for each workload/image:
kubectl get vulnerabilitymanifestsummaries -A

# View vulnerabilities **detailed** report for each workload/image:
kubectl get vulnerabilitymanifests -A
```