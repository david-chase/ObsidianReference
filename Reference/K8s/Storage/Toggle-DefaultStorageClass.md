#powershell #script #k8s #storage
# Toggle-DefaultStorageClass.ps1

This PowerShell script allows you to manage the default status of Kubernetes StorageClasses.

## Features

- **No Parameter Provided:** Lists all StorageClasses and indicates which one is the default.
- **Parameter Provided:** Toggles the `is-default-class` annotation for the specified StorageClass.

## Script Summary

- Uses `kubectl` to fetch StorageClass information in JSON format.
- Checks for existence of the specified StorageClass.
- If found, toggles the annotation `storageclass.kubernetes.io/is-default-class` between `true` and `false`.
- If no name is given, displays all StorageClasses with their default status in a color-coded format.

## Usage

```powershell
# To list all StorageClasses and show which is default
.\Toggle-DefaultStorageClass.ps1

# To toggle a specific StorageClass
.\Toggle-DefaultStorageClass.ps1 -StorageClassName standard
```
