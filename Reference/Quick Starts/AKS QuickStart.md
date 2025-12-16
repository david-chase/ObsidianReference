#aks #k8s #howto #tutorial #howto #quickstart 

## Create the RG 

``` powershell
az group create `
  --name rg-aks-winlinux `
  --location canadacentral
```

## Create the cluster with a Linux nodepool

``` powershell
az aks create `
  --resource-group rg-aks-winlinux `
  --name aks-winlinux `
  --node-count 1 `
  --node-vm-size Standard_DS2_v2 `
  --network-plugin azure `
  --network-plugin-mode overlay `
  --generate-ssh-keys
```

