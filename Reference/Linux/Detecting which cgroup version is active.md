#memory #cgroups #linux #k8s #oom

Run this on your node or inside a privileged container:

stat -fc %T /sys/fs/cgroup

- Output `tmpfs` → 🧟 cgroup **v1**
- Output `cgroup2fs` → 🚀 cgroup **v2**