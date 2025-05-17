#helm #subchart #chart #deployment #k8s 

In many cases, charts require other charts in order to install the entire application. For example, your application may require a simple Redis cache. You may choose to use a pre-built Redis chart as a chart dependency rather than writing the Redis template files from scratch. This allows multiple applications to share common building blocks.

Chart dependencies are located in an optional **charts/** directory. Here is an example of what a Redis chart dependency might look like inside the chart directory:

**charts/  
└── redis  
    ├── Chart.yaml  
    ├── README.md  
    ├── templates  
    │   ├── NOTES.txt  
    │   ├── \_helpers.tpl  
    │   ├── configmap.yaml  
    │   ├── headless-svc.yaml  
    │   ├── redis-statefulset.yaml  
    │   ├── redis-svc.yaml  
    │   ├── redis-serviceaccount.yaml  
    │   └── secret.yaml  
    └── values.yaml**

Notice how the layout is the same as a regular chart - that’s because it is. A chart dependency is nothing more than a chart nested under the **charts/** directory. Every time you install this chart, the Redis chart will be installed as part of it.

In some scenarios, you may wish to keep the source code for your dependency charts outside of the current source tree. For example, the Redis chart might be used by more than one of your charts. When you need to make an update to the Redis chart, it’d be best if you didn’t need to copy-paste updates across multiple charts that depend on it.

Instead, you can add a dependencies section to your **Chart.yaml** file. Here’s an example of defining a remote source for the Redis chart dependency in **Chart.yaml**:

**dependencies:  
- name: redis  
  version: "10.6.14"  
  repository: "https://charts.bitnami.com/bitnami"**

This specifies that you want the **redis** chart, version **10.6.14**, located in the chart repository with root URL **https://charts.bitnami.com/bitnami** (more information on chart repositories later in this chapter).

After adding this to **Chart.yaml**, you can then use the **helm dependency update** command to update the contents of the **charts/** directory prior to running the installation:

**$ helm dependency update**

**Saving 1 charts  
Downloading redis from repo htt‌ps://charts.bitnami.com/bitnami  
Deleting outdated charts**

Then if you inspect the **charts/** directory, you should see the Redis chart dependency:

**$ ls charts/**

**redis-10.6.14.tgz**

You’ll notice that the chart obtained via **helm dependency update** is in a tarball format (**redis-10.6.14.tgz**) vs. directory format (**redis/**). Since charts can be installed from either tarball or directory, there is no requirement for chart dependencies to be extracted underneath the **charts/** directory.

We will dive deeper into chart dependencies, when it's appropriate to use them, and how to leverage them.