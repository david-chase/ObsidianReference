#bi #data #grafana

The **Infinity data source** in **Grafana** is a powerful plugin that allows you to query **arbitrary JSON, CSV, XML, or GraphQL data** from **any HTTP endpoint** (local or remote) and display it in Grafana dashboards.

---

### 🔹 What Is It Used For?

The Infinity data source is ideal when:

- You want to **visualize data from a REST API, GraphQL, or CSV feed** that Grafana doesn't natively support.
    
- You have **custom endpoints** returning structured data (e.g., from your internal systems).
    
- You’re pulling **static or dynamic data** from public or private web sources.
    

---

### 🔹 Key Features

|Feature|Description|
|---|---|
|🔗 Supports HTTP/S|Pulls data from any HTTP endpoint (no need for a backend plugin).|
|📄 Multiple formats|Handles JSON, CSV, XML, GraphQL.|
|🔍 Filtering & transformation|Supports JMESPath and inline data transformation.|
|🔁 Variable support|You can use Grafana variables in URLs and queries.|
|⏱️ Refresh-friendly|Queries are refreshable at dashboard intervals.|
|🔐 Auth options|Can include headers, bearer tokens, etc. for secured APIs.|

---

### 🔹 Supported Query Types

- **JSON / CSV / XML**: Parse structured or tabular data.
    
- **GraphQL**: Send GraphQL queries to any endpoint.
    
- **Inline Data**: Manually define data for quick visualizations (like mock data or static lists).
    
- **UQL / GROQ**: Optional query languages for advanced filtering and shaping.