#gpu #optimization #k8s #utiization

- Using vanilla Kubernetes scheduler that doesn't support fractional GPUs
	- No gang scheduling (related pods are scheduled together or not at all)
	- No fractional GPUs
	- Dynamic resource reclamation
- No GPU sharing strategy in place
- Pods being scheduled on GPU instances that require no GPU
- Workloads being scheduled on GPU nodes that are part of a long pipeline, and only a small part of that pipeline needs GPU
- MIG config is incorrect, pods requesting mig configs that don't exist