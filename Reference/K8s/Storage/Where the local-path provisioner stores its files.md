#rancher #storage #k8s #pv 


By default, the **Rancher Local Path Provisioner** stores the contents of PersistentVolumes under:

``` text
/opt/local-path-provisioner
```

Each PersistentVolume will have its own subdirectory inside that path, usually named after the PVC or PV.

### Example Structure:

``` text
/opt/local-path-provisioner/pvc-<uid>/
```

Where `<uid>` is the UID of the PVC object.