#helm #deployment #k8s #chart 

At the core of each Helm chart is the **templates/** directory. This directory contains a collection of files suffixed with **.yaml** (or **.yml**).

Here’s an example **templates/** directory layout:

templates/  
├── NOTES.txt  
├── \_helpers.tpl  
├── deployment.yaml  
├── hpa.yaml  
├── ingress.yaml  
├── service.yaml  
├── serviceaccount.yaml  
└── tests  
    └── test-connection.yaml

Ignore **NOTES.txt** and **_helpers.tpl** for now. We will briefly cover these later in this chapter, see: _"Other Files"_. You can also ignore the **tests/** directory, this will be covered in the _"Tests"_ section.

Let’s focus on files here with the **.yaml** extension. Normally the names of these files indicate which types of Kubernetes resources they represent. For example, **deployment.yaml** defines a Kubernetes Deployment object.

Let's take a look at **deployment.yaml**:

**apiVersion: apps/v1  
kind: Deployment  
metadata:  
  name: {{ include "mychart.fullname" . }}  
  labels:  
    {{- include "mychart.labels" . | nindent 4 }}  
spec:  
{{- if not .Values.autoscaling.enabled }}  
  replicas: {{ .Values.replicaCount }}  
{{- end }}  
  selector:  
    matchLabels:  
      {{- include "mychart.selectorLabels" . | nindent 6 }}  
  template:  
    metadata:  
    {{- with .Values.podAnnotations }}  
      annotations:  
        {{- toYaml . | nindent 8 }}  
    {{- end }}  
      labels:  
        {{- include "mychart.selectorLabels" . | nindent 8 }}  
    spec:  
      {{- with .Values.imagePullSecrets }}  
      imagePullSecrets:  
        {{- toYaml . | nindent 8 }}  
      {{- end }}  
      serviceAccountName: {{ include "mychart.serviceAccountName" . }}  
      securityContext:  
        {{- toYaml .Values.podSecurityContext | nindent 8 }}  
      containers:  
        - name: {{ .Chart.Name }}  
          securityContext:  
            {{- toYaml .Values.securityContext | nindent 12 }}  
          image: "{{ .Values.image.repository }}:{{ .Values.image.tag | default .Chart.AppVersion }}"  
          imagePullPolicy: {{ .Values.image.pullPolicy }}  
          ports:  
            - name: http  
              containerPort: 80  
              protocol: TCP  
          livenessProbe:  
            httpGet:  
              path: /  
              port: http  
          readinessProbe:  
            httpGet:  
              path: /  
              port: http  
          resources:  
            {{- toYaml .Values.resources | nindent 12 }}  
      {{- with .Values.nodeSelector }}  
      nodeSelector:  
        {{- toYaml . | nindent 8 }}  
      {{- end }}  
      {{- with .Values.affinity }}  
      affinity:  
        {{- toYaml . | nindent 8 }}  
      {{- end }}  
      {{- with .Values.tolerations }}  
      tolerations:  
        {{- toYaml . | nindent 8 }}  
      {{- end }}**

You might notice that this file has some resemblance to a regular YAML file, but with some other syntax, namely the presence of curly braces ("{" and "}"). This file is not treated as pure YAML, but as a YAML **template**. The file uses the syntax defined by the [Go language’s templating system](https://golang.org/pkg/text/template/). Go’s templating syntax contains a collection of keywords designed for generating dynamic content in various output formats. In the case of Helm, Go templating is used to generate YAML documents representing Kubernetes resources.

The content between the curly braces is used to generate dynamic YAML based on provided settings. Here is a simple example for the container image used for the deployment:

**image: "{{ .Values.image.repository }}:{{ .Values.image.tag | default .Chart.AppVersion }}"**

Considering the **values.yaml** present from the previous section, this template would be converted to the following YAML:

**image: "nginx:1.16.0"**

You can use **.Values** to access anything in the values, or **.Chart** to access information about the chart (name, version, etc.).

This templating also supports conditionals (**if**) and loops (**with**), that you might find in regular programming languages. You can use these to conditionally output certain YAML sections, or to iterate over a list of items.

There are also a handful of special functions, such as **toYaml**, which will convert an object to its raw YAML representation, **nindent**, which ensures indentation level, or **include**, which allows you to include YAML sections predefined in **_helpers.tpl** (to learn more, see the _"Other Files"_ section later in this chapter).