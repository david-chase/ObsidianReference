#k8s #ai #jobs

**Indexed Jobs** are a special type of Kubernetes Job that are tailor-made for **parallel workloads** like AI/ML training, simulations, or batch data processing where **each task (or "index") is part of a coordinated whole**.

---

## ✅ **Purpose of Indexed Jobs**

Indexed Jobs are designed to run a **fixed number of pods**, each with a **unique index**, which is passed to the container as an environment variable. This is useful when each pod must process a distinct part of a dataset or play a distinct role (e.g., rank 0 in distributed training).

---

## 🔧 **Key Features**

|Feature|Description|
|---|---|
|`completionMode: Indexed`|Tells Kubernetes this is an Indexed Job — each Pod gets an index (`JOB_COMPLETION_INDEX`)|
|**Unique Index**|Kubernetes ensures each pod has a unique index from `0` to `N-1`|
|**Restartable**|If a pod fails, Kubernetes retries **that same index**, not a new random pod|
|**Pod Awareness**|Your app can read `$JOB_COMPLETION_INDEX` to determine what data or task to perform|
|**SuccessPolicy** (1.33 GA)|You can define which indexes must succeed for the job to be considered complete|
|**backoffLimitPerIndex** (1.33 beta)|Set how many times _each index_ can fail before giving up, instead of the whole job failing|

---

## 🛠️ **How to Use Indexed Jobs**

### 1. Define a Job like this:

yaml

CopyEdit

`apiVersion: batch/v1 kind: Job metadata:   name: indexed-job-example spec:   completions: 5   parallelism: 5   completionMode: Indexed   template:     spec:       containers:         - name: worker           image: busybox           command: ["sh", "-c", "echo I am pod index $JOB_COMPLETION_INDEX"]       restartPolicy: Never`

### 2. Each pod gets an env var:

bash

CopyEdit

`$ echo $JOB_COMPLETION_INDEX 0  # Or 1, 2, 3, ...`

---

## 🤖 Common Use Cases

|Use Case|How Indexed Jobs Help|
|---|---|
|AI Training|Assign pod 0 as "master" and others as "workers" using `$JOB_COMPLETION_INDEX`|
|Sharded Data Processing|Each pod processes a specific data partition (e.g., `shard_$JOB_COMPLETION_INDEX.csv`)|
|Simulations|Run N simulations in parallel with consistent indexing|