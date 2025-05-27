#argocd #k8s #deployment 

kubectl patch application <app-name> -n argocd `
  --type=json `
  -p '[ { "op": "remove", "path": "/metadata/finalizers" } ]'