#helm #deployment #k8s #functions

- **default** - provide a default value when one is missing or empty
- **nindent** - indent every line with a specified amount of whitespace
- **trunc** - truncate a string to a specified number of characters
- **trimSuffix** - trim a suffix from a string
- **toJson** - convert an object to JSON string
- **toYaml** - convert an object to a YAML string
- **b64enc** - get the base64 encoding of a string
- **sha256sum** - get the SHA-256 digest of a string
- **lookup** - lookup resources in a running Kubernetes cluster

``` yaml
**apiVersion: v1  
kind: ConfigMap  
metadata:  
  name: {{ .Release.Name }}-configmap  
data:  
  mykey: {{ .Values.myval | upper | quote }}**
```