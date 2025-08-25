#k8s #namespace #kubectl 

``` powershell
kubectl get all --namespace <your namespace> -o json | `
  ConvertFrom-Json | `
  Select-Object -ExpandProperty items | `
  Where-Object { $_.metadata.finalizers } | `
  ForEach-Object {
    [PSCustomObject]@{
      Name        = $_.metadata.name
      Kind        = $_.kind
      Finalizers  = ($_.metadata.finalizers -join ', ')
    }
  }
```

``` powershell
kubectl patch <kind> <name> `
  -n <your-namespace> `
  -p '{"metadata":{"finalizers":[]}}' `
  --type=merge
```

