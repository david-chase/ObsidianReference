#cloud #k8s #security #bastion

A **bastion host** is a special-purpose server designed and configured to withstand attacks and provide secure access to a private network from an external network—usually the internet. It acts as a **gateway or bridge** between an untrusted zone (like the public internet) and a trusted zone (like a private internal network or cloud VPC).

### Key Features:

- **Hardened Security**: It's minimized and locked down (e.g., unnecessary services disabled, strict firewall rules) to reduce its attack surface.
    
- **Single Entry Point**: It's often the only server exposed to the internet in a private network, forcing all administrative access through it.
    
- **Jump Server Functionality**: Admins log into the bastion host first, and from there, use SSH (or RDP) to access internal servers that are otherwise unreachable from outside.
    
- **Audit and Logging**: It can log access attempts and commands for compliance and monitoring.
    

### Example Use Case in AWS:

- A Linux bastion host with a public IP is placed in a public subnet.
    
- Private EC2 instances sit in private subnets without public IPs.
    
- You SSH into the bastion, then SSH from the bastion into the private instances.
    

In Kubernetes, **bastion hosts** are commonly used to securely administer clusters that are **not directly exposed to the public internet**, such as those running in private networks, VPCs, or on-premises environments.

### Typical Uses of a Bastion Host in Kubernetes:

#### 1. **Secure `kubectl` Access**

Admins use the bastion host as a jump point to:

- SSH into the bastion
    
- Use `kubectl` or Helm from the bastion to access the cluster’s API server
    
- This is common when the Kubernetes API server has a **private IP only**, or is locked down by security groups or firewall rules
    

#### 2. **SSH Tunneling to Access the API Server**

Sometimes, the bastion acts as a proxy:

- Set up an SSH tunnel from your local machine to the bastion
    
- Forward port `6443` (default for the API server) through the bastion
    
- You can then run `kubectl` from your local machine as if the API server was accessible directly
    

#### 3. **Access to Internal Services (port-forwarding)**

If you need to debug services or pods running inside the cluster:

- SSH to the bastion
    
- Use `kubectl port-forward` or `kubectl exec` from the bastion to reach pods
    
- Useful in cases where services or pods are only accessible inside the cluster network
    

#### 4. **Node Access**

- Bastion hosts can also serve as the only SSH gateway to reach worker or control plane nodes (for manual debugging or log retrieval)
    

---

### Security Practices

- **No `kubectl` from outside**: Keep the Kubernetes API private and force access through the bastion.
    
- **Minimal permissions**: Bastion should not run cluster-wide privileged processes.
    
- **Session logging**: Record commands and session logs for auditing.
    
- **Short-lived access**: Use temporary credentials and automation (e.g., AWS SSM, SSH certs) to reduce exposure.