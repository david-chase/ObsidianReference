#chatgpt #script #powershell #k8s #kubeconfig

This PowerShell script allows you to manage and clean up your Kubernetes kubeconfig file.

## Features

- Lists all contexts with numbers
- Allows user to delete a selected context
- Deletes associated clusters and users automatically
- Option to delete all contexts (with confirmation)
- Gracefully exits if no contexts are found
- Adheres to strict PowerShell formatting conventions

## Requirements

- PowerShell 7+
- powershell-yaml module:
  ```powershell
  Install-Module powershell-yaml -Scope CurrentUser
  ```

## Example

```powershell
::: Clear-Kubeconfig.ps1 :::

Available contexts:
1: dev-cluster
2: staging-cluster
3: prod-cluster

Enter the number of the context to delete, or type 'all' to delete all contexts:
```

## Safety

- Asks for confirmation before bulk deletion.
- Automatically updates the kubeconfig file in-place.

---

Script is designed to be run on Windows or Linux PowerShell sessions.

## Full Script

```powershell
param (
    [string] $KubeConfigPath = "$env:USERPROFILE\.kube\config"
)

Write-Host ""
Write-Host "::: Clear-Kubeconfig.ps1 :::" -ForegroundColor Cyan
Write-Host ""

# Check if kubeconfig file exists
if ( -not (Test-Path -Path $KubeConfigPath) ) {
    Write-Host "Kubeconfig file not found at path: $KubeConfigPath"
    exit 0
} # END if ( -not (Test-Path -Path $KubeConfigPath) )

# Parse kubeconfig YAML
try {
    $kubeconfig = Get-Content -Path $KubeConfigPath -Raw | ConvertFrom-Yaml
} catch {
    Write-Host "Failed to parse kubeconfig. Ensure it's a valid YAML file." -ForegroundColor Red
    exit 1
} # END try/catch

# Extract contexts
$contexts = $kubeconfig.contexts
if ( -not $contexts -or $contexts.Count -eq 0 ) {
    Write-Host "No contexts found in kubeconfig."
    exit 0
} # END if ( -not $contexts -or $contexts.Count -eq 0 )

# Display context list
Write-Host "Available contexts:" -ForegroundColor Green
for ( $i = 0; $i -lt $contexts.Count; $i++ ) {
    Write-Host "$($i + 1): $($contexts[$i].name)"
} # END for ( $i = 0; $i -lt $contexts.Count; $i++ )

Write-Host ""
Write-Host "Enter the number of the context to delete, or type 'all' to delete all contexts:"
$input = Read-Host "Selection"

# Confirm "delete all"
if ( $input -eq "all" ) {
    $confirm = Read-Host "Are you sure you want to delete ALL contexts, clusters, and users? (yes/no)"
    if ( $confirm -ne "yes" ) {
        Write-Host "Aborted by user."
        exit 0
    } # END if ( $confirm -ne "yes" )

    $kubeconfig.contexts.Clear()
    $kubeconfig.clusters.Clear()
    $kubeconfig.users.Clear()

    Write-Host "All contexts, clusters, and users have been removed." -ForegroundColor Yellow
} elseif ( $input -match '^\d+$' -and [int]$input -ge 1 -and [int]$input -le $contexts.Count ) {
    $index = [int]$input - 1
    $contextName = $contexts[$index].name
    $clusterName = $contexts[$index].context.cluster
    $userName = $contexts[$index].context.user

    # Remove the selected context, and any associated cluster and user
    $kubeconfig.contexts = $kubeconfig.contexts | Where-Object { $_.name -ne $contextName }
    $kubeconfig.clusters = $kubeconfig.clusters | Where-Object { $_.name -ne $clusterName }
    $kubeconfig.users = $kubeconfig.users | Where-Object { $_.name -ne $userName }

    Write-Host "Removed context '$contextName', cluster '$clusterName', and user '$userName'." -ForegroundColor Yellow
} else {
    Write-Host "Invalid selection. Exiting."
    exit 0
} # END if/elseif/else

# Output updated kubeconfig back to file
try {
    $yaml = $kubeconfig | ConvertTo-Yaml
    Set-Content -Path $KubeConfigPath -Value $yaml -Encoding utf8
    Write-Host "kubeconfig updated successfully." -ForegroundColor Green
} catch {
    Write-Host "Failed to write updated kubeconfig." -ForegroundColor Red
    exit 1
} # END try/catch

Write-Host ""
```
