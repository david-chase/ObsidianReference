#workflows #definition #dev 

A **Directed Acyclic Graph (DAG)** is a type of graph that has the following properties:

1. **Directed**: Each edge (or connection) between nodes has a direction, pointing from one node to another.
    
2. **Acyclic**: There are no cycles — meaning it's impossible to start at one node and follow a sequence of directed edges that loops back to the starting node.
    

### Key Concepts

- **Nodes (Vertices)**: Represent entities or points in the graph.
    
- **Edges (Arrows)**: Represent directional relationships between nodes (e.g., `A → B` means there's a directed edge from node A to node B).
    
- **No Cycles**: For any path through the graph, you will never revisit the same node.
    

### Example Use Cases

- **Task Scheduling**: DAGs are used to model tasks with dependencies (e.g., in build systems like Make or CI/CD tools like Argo Workflows).
    
- **Data Pipelines**: Tools like Apache Airflow use DAGs to define workflows, where each step depends on previous steps.
    
- **Version Control**: Git internally models commits as a DAG, where each commit points to its parent(s).
    
- **Compilers**: Use DAGs for optimization passes over expressions and instructions.
    

### Simple Visualization

css

CopyEdit

`A → B → D  \       ↑   → C ----`

In this DAG:

- `A` leads to both `B` and `C`
    
- `B` and `C` both lead to `D`
    
- No path circles back to an earlier node — it’s acyclic.
