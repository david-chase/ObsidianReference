#k8s #helm #chart 

A **Helm umbrella chart** is a **parent Helm chart** that manages one or more **child charts** (also called **subcharts**) as dependencies. It's called an _"umbrella"_ because it groups multiple related services under one chart, allowing you to deploy and manage a full application stack as a single unit.

---

### 🧩 Purpose of an Umbrella Chart:

- To **deploy a collection of microservices or components** (e.g., frontend, backend, database) together.
    
- To **centralize values management** across multiple subcharts.
    
- To **simplify versioning** and deployment of complex applications.