#azure #cli #cloud

Login to Azure CLI
	`az login`
Log out of Azure CLI
	`az logout`
Create a resource group
	`az group create --name $RG --location $Region`
Delete a resource group without prompts 
	`az group delete --resource-group $RG -y`
Create an AKS cluster
	`az aks create --resource-group $RG --name $ClusterName --node-count 1 --generate-ssh-keys`
Export a kube config file
	`az aks get-credentials --resource-group <cluster rg> --name <clustername> --file ~\.kube\config`