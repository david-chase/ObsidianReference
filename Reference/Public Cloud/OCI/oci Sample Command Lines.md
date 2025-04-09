#oci #oracle #cli 

Scale a node pool
	`oci ce node-pool update --node-pool-id ocid1.nodepool.oc1..example --size 5`
Export kubeconfig
	`oci ce cluster create-kubeconfig --cluster-id ocid1.cluster.oc1.iad.aaaaaaaapfywp7l66qmestlapelzr7emv6kp53pxyyf3vt3v5chnchktnq7a --file $HOME/.kube/config --region us-ashburn-1 --token-version 2.0.0` 
Get a list of OCI images
	`oci compute image list --compartment-id <your_compartment_ocid> --region <your_region>`
