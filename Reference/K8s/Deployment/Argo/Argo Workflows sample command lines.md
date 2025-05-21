#argo #workflows #cli

``` powershell
argo submit hello-world.yaml    # submit a workflow spec to Kubernetes 
argo list                       # list current workflows 
argo get hello-world-xxx        # get info about a specific workflow 
argo logs hello-world-xxx       # print the logs from a workflow 
argo delete hello-world-xxx     # delete workflow
```