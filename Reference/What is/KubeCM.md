#tool #project #k8s #confighub 

https://github.com/sunny0826/kubecm

## Summary

KubeCM (short for _KubeConfig Manager_) is an open-source command-line tool that helps you manage Kubernetes kubeconfig files and switch contexts more easily. Instead of manually editing `~/.kube/config`, KubeCM lets you list, merge, switch, delete, and import configs using simple commands. It is especially useful when you work with many clusters.

## Key capabilities

### Managing multiple kubeconfig files

- Add/import kubeconfig files from any path
- Merge several kubeconfigs into one
- Delete kubeconfig entries
- Back up and restore your kubeconfig file

### Switching and inspecting contexts

- List all contexts in your kubeconfig
- Switch between contexts by name
- Show the current active context
- Rename contexts
- Validate kubeconfig contents

### Handling users, clusters, and contexts

- List users in your kubeconfig
- List clusters in your kubeconfig
- Remove or rename users and clusters
- Clean up unused entries

### Enhanced usability features

- Supports tab completion (bash, zsh, fish, PowerShell)
- Provides colored, easy-to-read context lists
- Works cross-platform (Linux, macOS, Windows)

## Typical use cases

- Developers/SREs managing many clusters
- Switching between EKS, GKE, AKS, Minikube, and kubeadm clusters
- Cleaning up messy kubeconfig files
- Backing up kubeconfig before experimenting with tools
