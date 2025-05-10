#helm #k8s #tool #cli

Add a helm repository
	`helm repo add kubecost https://kubecost.github.io/cost-analyzer/`
Refresh all repos repository
	`helm repo update`
Install a helm chart
	`helm install <localname> <repo>/<chartname> [-n <namespace>] [--create-namespace] [--version <specific version>] [-f values.yaml]`
Download and unpack a helm chart
	`helm fetch <repo/chartname> --untar`
	`helm fetch densify/kubex-automation-stack --untar`
See what values can be configured for the chart
	`helm show values bitnami/wordpress`
Search all repos for a chart's available versions
	`helm search repo <chartname> --versions`
Show info about a chart, including required Kubernetes version
	`helm show chart <repo>/<chartname>`
Show all installed Helm charts in all namespaces
	 `helm list --all-namespaces`
Deploy a local version of a Helm chart
	`helm install <chartname> <foldername> -f values.yaml`
	`helm install harbot .`
	`helm install --create-namespace -n densify -f values-edit.yaml kubex .\kubex-automation-stack\`