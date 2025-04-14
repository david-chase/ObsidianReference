#k8s #cli 

## General Purpose
Check Kubernetes server version.
	`kubectl version`
Show information about the current cluster
	`kubectl cluster-info`
Start the API proxy in the background
	`kubectl proxy &`
	Now go to a browser and hit http://127.0.0.1:8001/
Create a bearer token then hit the API server with it
	`$token=kubectl create token default`
	`$apiserver = kubectl config view | Select-String -Pattern "https"`
	`$apiserver = ( $apiserver -split "server: ",2 )[ 1 ]`
	`curl $apiserver --header "Authorization: Bearer $token" --insecure`
Create a namespace
	`kubectl create ns new-namespace-name` 
Apply a YAML manifest
	`kubectl apply -f <filename.yml>`
Show a list of pods, services, and endpoints
	`kubectl get po,svc,ep --show-labels`
Delete a namespace and all its contents
	`kubectl delete ns <namespace>`
Watch HPA behaviour in real time
	`kubectl get hpa nginx --watch`
Get a list of supported objects, short names, and their API versions
	`kubectl api-resources`
Get help info on an object type
	`kubectl explain pod`
Get help info on a subset of an object
	`kubectl explain pod.spec`
Show the differences a YAML file would make if it were applied
	`kubectl diff -f <filename>`
Copy files from a pod to your local machine
	`kubectl exec <pod-name> -- tar cf - /path/in/container | tar xf - -C /local/path`
Copy files from your local machine to a pod
	`tar cf - /local/path | kubectl exec -i <pod-name> -- tar xf - -C /path/in/container`

## ConfigMaps
Show a list of configmaps
	`kubectl get configmaps`
	`kubectl get configmaps configmap-name -o yaml`
Create a configmap from a yaml file
	`kubectl create -f configmap.yaml`
Show the contents of a configmap (export)
	`kubectl describe configmap densifyconf --namespace densify`

## Configuration
Show the contents of the kubectl config file
	`kubectl config view`
Set the current context
	`kubectl config set-context --current --namespace=test-nginx`
Delete a context from kube config
	`kubectl config delete-context <contextname>`
Delete a user from kube config
	`kubectl config delete-user <user>`
Delete a cluster from kube config
	 `kubectl config delete-cluster <cluster>`

## Daemonsets
Show daemonsets running in all namespaces
	kubectl get ds -A

## Deployments
Scale a deployment to 3 replicas
	`kubectl scale deploy <deploymentname> --replicas=3`
Show the rollout history of a deployment
	`kubectl rollout history deploy <deploymentname>`
	`kubectl rollout history deploy <deploymentname> --revision=<revnum>`
Create a deployment
	`kubectl create deployment <deployment name> --image=pbitty/hello-from:latest --port:80 --replicas=3`
Scale or pause a deployment
	`kubectl scale deployment/prometheus-kube-state-metrics --replicas=0 -n monitoring`
Connect to a deployment from your browser
	`kubectl describe deploy/<name> (write down the port it's on)`
	`kubectl port-forward deploy/<name> <externalport>:<internalport>`
	`http://localhost:<externalport>`

## Events
Show all the events in a namespace (helps for troubleshooting if the logs say nothing)
	`kubectl get events --sort-by=.metadata.creationTimestamp -n <namespace>`
Get events for a namespace
	`kubectl events -n <namespace>`
Get events for all namespaces
	`kubectl events --all-namespaces`
Get events for a pod
	`kubectl events --for pod/data-forwarder -n densify`
Delete all events in a namespace
	`kubectl delete events --all -n <namespace>`

## Karpenter
Export a Karpenter nodepool definition
	`kubectl get nodepool <nodepool-name> -o yaml > <output filename>.yaml`
Export a Karpenter nodeclass defintion
	`kubectl get nodeclass <nodeclass-name> -o yaml > <output filename>.yaml`
Delete a nodepool
	`kubectl delete nodepool <nodepool-name>`
List all nodepools
	`kubectl get nodepool`
List all nodeclaims
	`kubectl get nodeclaim`

## Labels & Annotations
Show a list of deployments, replicasets, and pods with label "app=testapp"
	`kubectl get deploy,rs,po -l app=testapp`
Show all pods with a particular label and value
	`kubectl get pods -l k8s-app=web-dash`
Show all pods with a particular label
	`kubectl get pods -L k8s-app,label2`
Get all labels assigned to a pod
	`kubectl get <podname> -o json | jq .metadata.labels`
Get all annotations for a node
	`kubectl get node <nodename> -o json | jq .metadata.annotations`
Apply a label to a pod, overwriting if it exists
	`kubectl label pod <podname> <key>=<value> --overwrite`
	
## Logs
Show logs for a pod
	`kubectl logs densify`

## Nodes
Show a list of nodes, pods or namespaces
	`kubectl get nodes`
	`kubectl get pods`
	`kubectl get pods --all-namespaces`
	`kubectl get pods -A`
	`kubectl get ns`
Find out what node a pod is running on
	`kubectl describe pod -n <namespace> <pod long name> | grep Node:`
Find out what pods are running on what nodes
	`kubectl describe nodes`
Add a label to a node
	`kubectl label nodes minikube clusterRole=master`
Drain a node
	`kubectl drain --ignore-daemonsets <node name>`
Check max pods per node
	`kubectl get node <node_name> -ojsonpath='{.status.capacity.pods}'`
Enable VPC Prefix (maximize pods per node on EKS, must be on Nitro hypervisor)
	`kubectl set env daemonset aws-node -n kube-system ENABLE_PREFIX_DELEGATION=true`
Check MaxPodsPerNode
	`kubectl get node <node_name> -ojsonpath='{.status.capacity.pods}'`
Corden or drain a node
	`kubectl drain node <node_name> --ignore-daemonsets --delete-emptydir-data`
Uncorden a node
	`kubectl uncordon <node_name>`

## Pods
Show a list of pods only in a single namespace
	`kubectl get pods -n kube-system`
	`kubectl get pods -n densify`
Create a pod without a manifest
	`kubectl run <podname> --image=nginx`
Create a manifest for a pod
	`kubectl run <podname> --image=nginx --port=80 -dry-run=client -o yaml > manifest.yml`
Get info about a pod including events
	`kubectl describe pods <podname>`
Replace a running pod
	`kubectl replace --force -f <filename.yml>`
Delete a pod
	`kubectl delete -f nginx.yml`
	`kubectl delete pods <podname1> <podname2>`
Get the QoS class for a pod
	`kubectl --namespace=qos-example get pod qos-demo-4 -o jsonpath='{ .status.qosClass}{"\n"}'`
Show containers with any restarts
	`kubectl get pods --all-namespaces --sort-by='.status.containerStatuses[0].restartCount'`
Show containers with more than 3 restarts
	`kubectl get pods --all-namespaces --field-selector=status.containerStatuses.restartCount>3`
Show all pods not in a "Running" state
	`kubectl get pods --all-namespaces --field-selector=status.phase!=Running`
Display CPU & memory usage of all running pods in a namespace
	`kubectl top pod -n <namespace>`


## Services
Run a pod and also expose it as a service
	`kubectl run pod-hello --image=pbitty/hello-from:latest --port=80 --expose=true`
Edit a service
	`kubectl edit svc <service-name>`
Expose an existing deployment as a service
	`kubectl expose deployment webserver --name=web-service --type=NodePort`
	`kubectl expose deployment <deployment_name> --type=<service_type> --port=<port>`
Forward a port to a service
	`kubectl port-forward -n monitoring service/prometheus-kube-prometheus-prometheus 4090:9090`
	`http://localhost:4090`
	`kubectl port-forward -n monitoring service/prometheus-grafana 3030:80`
Connect to a service
	`kubectl service test-nginx-svc -n test-nginx`
	
## Storage
List the available storage classes, as well as the default
	`kubectl get storageclass` 
Patch a storageclass to be default
	 `kubectl patch storageclass <StorageClassName> -p @{ metadata = @{ annotations = @{ "storageclass.kubernetes.io/is-default-class" = "true" } } } --type=merge`


## Troubleshooting
Launch a temporary debugging pod that auto-deletes when you exit.
	`kubectl run debug-pod --rm -it --image=busybox -- /bin/sh`
Run a command on a pod or service
	`kubectl exec -it <podname> -- <command>`
	`kubectl exec -it svc/servicename -- <command>`
Open a shell on a container
	`kubectl exec -it <podname> -c <containername> -- /bin/sh`
Test external connectivity
	`kubectl run test-pod --rm -it --image=busybox -- /bin/sh`
	`ping partner1.densify.com`
See events for a pod or object
	 `kubectl describe <object-type> <object-name>`
	 `kubectl descript po nginx -n webserver`
