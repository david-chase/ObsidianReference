#aws #cli #cloud 

Add an EKS cluster to kubeconfig
	`aws eks update-kubeconfig --region us-east-2 --name k8master`
List my AWS CLI profiles
	`aws configure list-profiles`
Change to another AWS profile
	`aws configure --profile <daas|salesdemo>`
Switch between AWS profiles
	`export AWS_PROFILE=profile_name (Linux)`
	`$Env:AWS_PROFILE = '<profilename>'`
Add a cluster to kubeconfig
	`aws eks --region <region> update-kubeconfig --name <cluster_name>`
Scale an ASG
	`aws autoscaling update-auto-scaling-group --desired-capacity <desired> --auto-scaling-group-name <asg-name> --min-size <minsize> --max-size <maxsize>`
Set the KUBERNETES_MASTER environment variable
	`$env:KUBERNETES_MASTER = (aws eks describe-cluster --name <clustername> --query "cluster.endpoint" --output text)`
