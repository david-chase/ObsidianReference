#helm #k8s #tool #cli

Add a helm repository
	`helm repo add kubecost https://kubecost.github.io/cost-analyzer/`
Refresh all repos repository
	`helm repo update`
Install a helm chart
	`helm install <localname> <repo>/<chartname> [-n <namespace>] [--create-namespace] [--version <specific version>] [-f values.yaml]`
Download and unpack a helm chart
	`helm fetch <harbor/harbor> --untar`
See what values can be configured for the chart
	`helm show values bitnami/wordpress`
Search all repos for a chart's available versions
	`helm search repo <chartname> --versions`
Show info about a chart, including required Kubernetes version
	`helm show chart <repo>/<chartname>`
