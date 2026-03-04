#k8s #redhat #distribution 

[[OpenSearch]]

```table-of-contents
```
## OpenShift general notes

- Uses the command `oc` instead of `kubectl`
- Splits system components into multiple namespaces named `openshift-*`
- Uses its own CNI [[OVN-Kubernetes]]

## OpenShift Managed Platforms

| **Managed Offering**                    | **Cloud Service Provider**  | **Management Model**                     |
| --------------------------------------- | --------------------------- | ---------------------------------------- |
| Red Hat OpenShift Service on AWS (ROSA) | Amazon Web Services (AWS)   | Jointly managed by Red Hat and AWS       |
| Azure Red Hat OpenShift (ARO)           | Microsoft Azure             | Jointly managed by Red Hat and Microsoft |
| Red Hat OpenShift on IBM Cloud          | IBM Cloud                   | Managed by IBM                           |
| Red Hat OpenShift Dedicated (OSD)       | AWS / Google Cloud Platform | Managed by Red Hat                       |