#redhat #product #iac #configuration 

https://www.ansible.com

**Ansible** is an open-source IT automation and configuration management tool developed by Red Hat. It allows you to automate tasks such as application deployment, configuration management, orchestration, and provisioning across your infrastructure. Ansible is known for being **agentless**, using **SSH (or WinRM for Windows)** to connect to managed systems, and for its **declarative YAML-based playbooks**.

---

### 🔑 **Key Features**

- **Agentless Architecture**  
    No software needs to be installed on the target machines—uses SSH (Linux) or WinRM (Windows).
    
- **YAML-Based Playbooks**  
    Uses simple, human-readable YAML syntax for defining automation tasks.
    
- **Idempotency**  
    Ensures tasks are only applied when needed—running the same playbook multiple times produces the same result.
    
- **Inventory Management**  
    Tracks target hosts using static files, dynamic inventory scripts, or plugins (e.g., AWS, Azure, VMware).
    
- **Modules Library**  
    Comes with hundreds of built-in modules for managing files, packages, services, cloud resources, etc.
    
- **Extensibility**  
    Supports custom modules, plugins, roles, and collections to extend capabilities.
    

---

### 🧱 **Typical Use Cases**

- Provisioning cloud instances (e.g., AWS EC2, Azure VMs)
- Installing and configuring applications (e.g., NGINX, PostgreSQL)
- Enforcing security policies and configurations
- Orchestrating complex multi-tier deployments
- Patching operating systems
- CI/CD pipeline integrations
    

---

### 📦 **Core Components**

- **Playbooks:** YAML files that describe the desired state of systems.
- **Inventory:** List of machines to manage (INI, YAML, or dynamic).
- **Modules:** Reusable scripts for performing specific tasks (e.g., `copy`, `yum`, `apt`, `service`).
- **Roles:** A way to group tasks, handlers, templates, and variables in a reusable structure.
- **Collections:** Distributable content bundles with roles, modules, and plugins.