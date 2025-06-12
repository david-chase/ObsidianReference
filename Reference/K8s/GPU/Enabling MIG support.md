#mig #k8s #nvidia #gpu 

- Requires the NVidia GPU operator
- Specifically the NVidia Device Plugin 
- Provisioning is done by `nvidia-mig-parted`
- You create the mig configs you want in a ConfigMap, then you create a node label to determine which MIG config is applied to that node.

``` yaml
version: v1  
mig-configs:  
all-disabled:  
- devices: all  
mig-enabled: false  
  
all-enabled:  
- devices: all  
mig-enabled: true  
mig-devices: {}  
  
all-1g.5gb:  
- devices: all  
mig-enabled: true  
mig-devices:  
"1g.5gb": 7  
  
all-2g.10gb:  
- devices: all  
mig-enabled: true  
mig-devices:  
"2g.10gb": 3  
  
all-3g.20gb:  
- devices: all  
mig-enabled: true  
mig-devices:  
"3g.20gb": 2
```

- If a pod requests a specific mig config and there is no node labelled with that mig config, then the pod will go unscheduled