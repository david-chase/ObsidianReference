#helm #k8s #deployment #values

If you pass a custom values file to helm ( "-f values-edit.yaml") Helm will *first* process values.yaml if it exists, then overlay your custom values-edit.yaml on top.

### ✅ **How it works:**

- The default `values.yaml` inside the chart is **always loaded first**.
- Then, your `-f values-prod.yaml` file is loaded and **merged on top**, overriding any matching keys.
    

---

### 🔁 Merge Order (in precedence):

1. `values.yaml` (default inside the chart)
2. Files passed with `-f` (in order of appearance)
3. `--set` flags (inline overrides)