#minikube #cli #k8s 

Show list of clusters and config
	`minikube profile list`
Delete the current cluster
	`minikube delete`
Delete a specific cluster
	`minikube delete -p <clustername>`
Create a cluster
	`minikube start -n <number of nodes> --kubernetes-version=v1.25.3 --driver=virtualbox --container-runtime=containerd --cni=calico`
Show status of control plane and worker nodes
	`minikube status`
Show a list of minikube addons
	`minikube addons list`
Enable an addon
	`minikube addons enable dashboard`
	`minikube addons enable <addon name>`
Start the kubernetes dashboard, alternately just get the URL to paste into a browser
	`minikube dashboard`
	`minikube dashboard --url`
Show services running in the cluster
	`minikube service --all`
Connect to a service
	`minikube service test-nginx-svc -n test-nginx`
Mount a host filesystem in guest VM
	`minikube mount <host folder>:<guest folder>`
	`minikube mount /mnt/densify:/mnt/densify`
SSH into a node
	`minikube ssh -n <node>`
Show nodes and their IP addresses
	`minikube node list`
