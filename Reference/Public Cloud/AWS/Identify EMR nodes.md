#emr #aws #cloud 

## **How EMR Identifies Its Nodes**

When you launch an EMR cluster, AWS automatically adds **EC2 tags** and **instance metadata**. These let you distinguish between EMR-related instances and other workloads.

### **Default Tags EMR Adds**

- **`aws:elasticmapreduce:instance-group-role`**
    - Values: `MASTER`, `CORE`, or `TASK`
    - Lets you immediately see what role the node is playing in the cluster.
- **`aws:elasticmapreduce:job-flow-id`**
    - The unique cluster (job flow) ID, e.g., `j-XXXXXXXXXXXXX`.
    - Useful to tie nodes back to a specific cluster.
- **`aws:elasticmapreduce:instance-group-id`**
    - Identifies the instance group or instance fleet that the node belongs to.