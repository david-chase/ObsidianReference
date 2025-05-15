#helm #deployment #k8s 

# Functions and Pipelines

---

Helm templates are based upon the Go language’s templating language, but they also benefit from some extra added features in the form of **functions** and **pipelines**. Functions and pipelines enhance the overall capabilities of templates, enabling common tasks, such as modifying the output format of a configuration value.   

Template **functions** follow the following syntax:

**functionName arg1 arg2**…

As a basic example, let’s say we want to make sure that some of our numeric/integer values are parsed in our YAML file as strings versus as numbers. For this, we can use the **quote** function. The **quote** function ensures that double quotes are around a given value. Here is an example:

**apiVersion: v1  
kind: ConfigMap  
metadata:  
  name: {{ .Release.Name }}-configmap  
data:  
  mykey: {{ quote .Values.myval }}**

Template **pipelines**, on the other hand, follow the following syntax:

**arg1 | functionName**

Pipelines are essentially the same as functions, but they are invoked differently. Pipelines act similar to UNIX pipes, in that you are able to chain together multiple functions to achieve the desired result.

Here’s the same example as the above, but this time using the **quote** function in pipeline form:

**apiVersion: v1  
kind: ConfigMap  
metadata:  
  name: {{ .Release.Name }}-configmap  
data:  
  mykey: {{ .Values.myval | quote }}**

Here’s another example: you have an existing app that only accepts configuration in all uppercase letters. This is a perfect case for pipelines, as we can just add the **upper** function on the same line:

**apiVersion: v1  
kind: ConfigMap  
metadata:  
  name: {{ .Release.Name }}-configmap  
data:  
  mykey: {{ .Values.myval | upper | quote }}**

You can use any number of pipelines chained together.

Here’s a short list of functions commonly used in Helm chart templates:

- **default** - provide a default value when one is missing or empty
- **nindent** - indent every line with a specified amount of whitespace
- **trunc** - truncate a string to a specified number of characters
- **trimSuffix** - trim a suffix from a string
- **toJson** - convert an object to JSON string
- **toYaml** - convert an object to a YAML string
- **b64enc** - get the base64 encoding of a string
- **sha256sum** - get the SHA-256 digest of a string
- **lookup** - lookup resources in a running Kubernetes cluster

In total, there are over 60 available functions available to Helm templates, some of them coming from the Go language built-in functions, and others from a third-party library called [**sprig**](https://masterminds.github.io/sprig/).

# Conditional Blocks (**if**/**else**)

---

In many cases, certain sections of YAML should only be present given the presence of a given configuration setting. You may even want to exclude an entire resource altogether.

In Helm templates, this can be achieved using an **if** / **else** block. Here is a simple example:

**apiVersion: v1  
kind: ConfigMap  
metadata:  
  name: {{ .Release.Name }}-configmap  
data:  
  mykey: "abc"  
  {{- if eq .Values.metrics "enabled" }}  
  useMetrics: "true"  
  {{- end }}**

In the above example, the **useMetrics** key of the ConfigMap is set to **"true"** only if the metrics value equals the string **"enabled"**.

You can also use the **else** keyword to do something if the condition is not met, such as set **useMetrics** to **"false"**:

**apiVersion: v1  
kind: ConfigMap  
metadata:  
  name: {{ .Release.Name }}-configmap  
data:  
  mykey: "abc"  
  {{- if eq .Values.metrics "enabled" }}  
  useMetrics: "true"  
  {{- else }}  
  useMetrics: "false"  
  {{- end }}**

Lastly, you can use the **else if** keyword any number of times to do something else if some other condition is satisfied instead of the original:

**apiVersion: v1  
kind: ConfigMap  
metadata:  
  name: {{ .Release.Name }}-configmap  
data:  
  mykey: "abc"  
  {{- if eq .Values.metrics "enabled" }}  
  useMetrics: "true"  
  {{- else if eq .Values.metrics "disabled" }}  
  useMetrics: "false"  
  {{- else }}  
  useMetrics: "unknown"  
  {{- end }}**

Compound conditionals are also supported by using the **or** and **and** keywords. For example, maybe you only want to set **useMetrics** to **true** if either one of two conditions are met. This can be achieved with the **or** keyword:

**apiVersion: v1  
kind: ConfigMap  
metadata:  
  name: {{ .Release.Name }}-configmap  
data:  
  mykey: "abc"  
  {{- if or (eq .Values.metrics "enabled") (eq .Values.metrics "true") }}  
  useMetrics: "true"  
  {{- end }}**

In the example above, if the operator installing the chart sets the **metrics** configuration setting to the string **true** vs. **enabled**, they will still achieve the desired result.

In the inverse, you may wish to make your conditional stricter, by requiring multiple conditions to be met. This can be achieved with the **and** keyword:

**apiVersion: v1  
kind: ConfigMap  
metadata:  
  name: {{ .Release.Name }}-configmap  
data:  
  mykey: "abc"  
  {{- if and (eq .Values.metrics "enabled") (gt (int .Values.port) 1023) }}  
  useMetrics: "true"  
  {{- end }}**

In the example above, the **useMetrics** key in the **ConfigMap** will only be set to **true** if both the **metrics** value equals the string **enabled** and the **port** value is greater than 1023 (1024 or above).

You can combine any number of **and** and **or** statements to formulate complex conditionals, however it's best to keep this as simple as possible to make the installation process straightforward for the operator.

In the examples above, we saw the **eq** and **gt** keywords - these are comparison operators. Here is a list of all of the available comparison operators you can use in conditionals:

- **eq** (equals) - “does the value of the left equal the value on the right?”  
    
    - Example, “if the cache type is Redis”:  
        **eq .Values.cacheType "redis"**
    
- **ne** (not equals) - “does the value of the left not equal the value on the right?”  
    
    - Example, “if debug mode is not enabled”:  
        ****ne .Values.debugMode "enabled"****
    
- **lt** (less than) - “is the value of the left less than the value on the right?”  
    
    - Example, “if max clients is less than 50”:  
        ****lt .Values.maxClients 50****
    
- **gt** (greater than) - “is the value of the left greater than the value on the right?”  
    
    - Example, “if max clients is greater than 150”:  
        ****gt .Values.maxClients 150****

# Loops (**range**)

---

In some cases, you may wish to repeat sections of YAML. For example, let’s say you want to combine a list of friends into a single string from a list of values. To achieve this, you can use a range block. A range block can iterate over either an array (e.g. **[item1, item2, …]**) or an object/map (e.g. **{a: 1, b: 2, …}**).

Consider you have the following section in **values.yaml**:

**friends:  
- stephanie  
- paul  
- eddie**

This is an example of an array. If you wanted to place each of these names into a single section, separated by a space, you could do something like the following:

**apiVersion: v1  
kind: ConfigMap  
metadata:  
  name: {{ .Release.Name }}-configmap  
data:  
  friends: "{{ range .Values.friends }}{{ . }} {{ end }}"**

which would result in the following:

**apiVersion: v1  
kind: ConfigMap  
metadata:  
  name: myrelease-configmap  
data:  
  friends: "stephanie paul eddie"**

Notice the use of the single period **{{ . }}** - for range loops on arrays, this represents the current value (i.e. **stephanie** on the first iteration, **paul** on the second, etc.). If you need to access the current index of the array, you can use an alternate syntax:

**apiVersion: v1  
kind: ConfigMap  
metadata:  
  name: {{ .Release.Name }}-configmap  
data:  
  friends: "{{ range $i, $e := .Values.friends }}{{ $i }}_{{ $e }} {{ end }}"**

which would result in the following:

**apiVersion: v1  
kind: ConfigMap  
metadata:  
  name: myrelease-configmap  
data:  
  friends: "0_stephanie 1_paul 2_eddie"**

Keep in mind that the variable names for the index and the associated element are up to you (meaning, **$i** and **$e** from the example could be named whatever you want).

Next, consider you have the following section **values.yaml**:

**env:  
  DEBUG: 1  
  PORT: 8080  
  EXTERNAL_URL: https://mysite.com**

This is an example of a map/object. If you wanted to use this object to populate the environment variables for a given Pod container, you could do the following:

**apiVersion: v1  
kind: Pod  
metadata:  
  name: {{ .Release.Name }}-pod  
spec:  
  containers:  
  - image: busybox  
    name: hello  
    command: ['sh', '-c', 'env && sleep 3600']  
    env:  
    {{- range $key, $value := .Values.env }}  
    - name: {{ $key }}  
      value: {{ $value | quote }}  
    {{- end }}**

which would result in the following:

**apiVersion: v1  
kind: Pod  
metadata:  
  name: myrelease-pod  
spec:  
  containers:  
  - image: busybox  
    name: hello  
    command: ['sh', '-c', 'env && sleep 3600']  
    env:  
    - name: DEBUG  
      value: "1"  
    - name: PORT  
      value: "8080"  
    - name: EXTERNAL_URL  
      value: "https://mysite.com"**

Notice the use of **$key** and **$value** - for range loops on objects, these variables are set to the key and value for the current item in the loop (i.e. **$key** = **DEBUG** and **$value** = **1** on the first iteration, etc.). These variables can also be named whatever you want, such as **$k** and **$v** (or anything else).

These are very basic examples of loops, but you could loop over any YAML section, including entire Kubernetes resources. For example, a loop in a single template file could produce multiple **ConfigMap** resources.

# Variable Scope (**with**)

---

At any point in a Helm template, there is an implicit scope, or context. The current scope is always defined by a single period (**.**). When we reference things such as **{{ .Values }}** within a template, we are accessing the **Values** object in the current scope.

You may wish to modify the current scope in certain situations, for example, to simplify a section of your template that uses many subfields of a value with a long name. Instead of referencing **.Values.business.office.address.street** and **.Values.business.office.address.city**, you could use **.street** and **.city** by changing the scope to start at **.Values.business.office.address**.

This can be achieved using a **with** block.

Let’s say we have the following section in **values.yaml**:

**address:  
  street: 1917 Melrose Street  
  city: Millwood  
  state: WA  
  zip: 99212**

Here is an example of modifying scope in a template to access this data:

**apiVersion: v1  
kind: ConfigMap  
metadata:  
  name: {{ .Release.Name }}-address  
data:  
  {{- with .Values.address }}  
  fullAddress: "{{ .street }}, {{ .city }}, {{ .state }} {{ .zip }}"  
  {{- end }}**

which would result in the following:

**apiVersion: v1  
kind: ConfigMap  
metadata:  
  name: myrelease-address  
data:  
  fullAddress: "1917 Melrose Street, Millwood, WA 99212"**

Keep in mind that inside of a **with** block, you do not have access to the previous scope. For example, you will not be able to access things such as **{{ .Values }}** or **{{ .Release }}**.

# Partials and Includes

---

In many cases, you will want to repeat certain sections of YAML within your templates. To achieve this, you can use **partials**.

By convention, partials should go into a special file called **_helpers.tpl** within the **templates/** directory.

Here is an example **_helpers.tpl** file containing a partial for custom annotations:

**{{- define "mychart.annotations" -}}  
x: 1  
y: 2  
z: 3  
{{- end }}**

Notice the **define** keyword used to define the partial.

To use this partial in a template, you can use the **include** keyword:

**apiVersion: v1  
kind: Pod  
metadata:  
  name: {{ .Release.Name }}-pod  
  annotations:  
    {{- include "mychart.annotations" . | nindent 4 }}  
spec:  
  containers:  
  - image: busybox  
    name: hello  
    command: ['sh', '-c', 'env && sleep 3600']**

The resulting YAML of this template is the following:

**apiVersion: v1  
kind: Pod  
metadata:  
  Name: myrelease-pod  
  annotations:  
    x: 1  
    y: 2  
    z: 3  
spec:  
  containers:  
  - image: busybox  
    name: hello  
    command: ['sh', '-c', 'env && sleep 3600']**

Partials can be included in the same way across multiple templates, making them a very powerful tool to reduce the overall size of your Helm chart.

# Whitespace and Newlines

---

One of the biggest challenges using Helm (and YAML in general) is trying to ensure the correct amount of whitespace and newlines to create a valid YAML document. There are a couple aspects of Helm templates worth mentioning to help mitigate this issue.

First, let's take a look at a simple template:

**apiVersion: v1  
kind: ConfigMap  
metadata:  
  name: {{ .Release.Name }}-configmap  
data:  
  mykey: {{ .Values.myval }}  
  other: otherval**

The expected result of this is something like the following:

**apiVersion: v1  
kind: ConfigMap  
metadata:  
  name: myrelease-configmap  
data:  
  mykey: someval  
  other: otherval**

Not let’s make a simple change, by replacing some open curly braces ( **{{** ) with open curly braces followed by a dash/hyphen ( **{{-** ):

**apiVersion: v1  
kind: ConfigMap  
metadata:  
  name: {{ .Release.Name }}-configmap  
data:  
  mykey: {{- .Values.myval }}  
  other: otherval**

The result of this change is something like the following:

**apiVersion: v1  
kind: ConfigMap  
metadata:  
  name: myrelease-configmap  
data:  
  mykey:someval  
  other: otherval**

which is invalid YAML! So what changed?

When you use curly braces within templates, by placing a dash (-) on open or close, it will trim all preceding or following whitespace (including newlines).

Let’s see what happens if we place the dash on the other side ( **-}}** ):

**apiVersion: v1  
kind: ConfigMap  
metadata:  
  name: {{ .Release.Name }}-configmap  
data:  
  mykey: {{ .Values.myval -}}  
  other: otherval**

This results in the following:

**apiVersion: v1  
kind: ConfigMap  
metadata:  
  name: myrelease-configmap  
data:  
  mykey: somevalother: other**

This is also invalid YAML!

In many cases, trimming whitespace might actually be the desired result. For example, you may want to prevent extra newlines in your YAML when starting an **if**, **range**, or **with** section. Just make sure you’re only using the dashes when you mean to as not to have unintended side effects.

Another useful tool to mitigate whitespace issues is the **nindent** functions. **nindent** enforces a fixed number of spaces before the line starts. What’s nice about this is that if you accidentally mess up and put the wrong amount of indentation for a given section, it will still be enforced.

Here’s an example of a template using **nindent**:

**apiVersion: apps/v1  
kind: Deployment  
metadata:  
  name: {{ include "mychart.fullname" . }}  
  labels:  
    {{- include "mychart.labels" . | nindent 4 }}  
...**

which would result in something like the following:

**apiVersion: apps/v1  
kind: Deployment  
metadata:  
  name: myrelease-mychart  
  labels:  
    helm.sh/chart: mychart-0.1.0  
    app.kubernetes.io/name: x  
    app.kubernetes.io/instance: myrelease  
    app.kubernetes.io/version: "1.16.0"  
    app.kubernetes.io/managed-by: Helm  
...**

Now let’s say we forget to indent the labels section correctly:

**apiVersion: apps/v1  
kind: Deployment  
metadata:  
  name: {{ include "mychart.fullname" . }}  
  labels:  
{{- include "mychart.labels" . | nindent 4 }}  
...**

Now let’s say we forget to indent the labels section. Because we are using **nindent**, the resulting YAML is exactly the same:

**apiVersion: apps/v1  
kind: Deployment  
metadata:  
  name: myrelease-mychart  
  labels:  
    helm.sh/chart: mychart-0.1.0  
    app.kubernetes.io/name: x  
    app.kubernetes.io/instance: myrelease  
    app.kubernetes.io/version: "1.16.0"  
    app.kubernetes.io/managed-by: Helm  
...**

This is extremely useful and will prevent headache due a number of factors, including your text editor attempting to make indentations for you. Of course, it is still best to keep the indentations aligned correctly so the template is easier for others to read.