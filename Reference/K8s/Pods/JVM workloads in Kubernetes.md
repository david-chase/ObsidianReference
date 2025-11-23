#jvm #k8s #containers 

### Manifests

``` yaml
resources:  
	requests:  
		cpu: 1000m  
		memory: 2Gi              # Set mem. requests = limits for JVM
	limits:  
		cpu: 1000m               # This determines how many CPUs JVM will "see"
		memory: 2Gi              # Mem limits are used to determine heap size
args:  
	- "-XX:MaxRAMPercentage=75"  # This is preferred to hard-coding -Xms or -Xmx
	- "-jar"  
	- "/app/jvm-stats.jar"
```

- Using -XX:MaxRAMPercentage is preferable to hard-coding -Xms or -Xmx as it will respons appropriately to changes in your pod requests and limits.  i.e. - For different sized containers for prod and dev
- **XX:MaxRAMPercentage=75** or even 85 are good settings
- At the very least assign **the same memory requests and limits** to your container.
- To hard code which garbage collector to use, specify either **-XX:+UseG1GC** or **-XX:+UseZGC -XX:+ZGenerational**
- G1GC and ZGC are considered the more modern GCs
- In this example JVM will not attempt to multi-thread because it only "sees" one processor

#### Overriding CPU limits

``` yaml
resources:  
	requests:  
		cpu: 1000m  
		memory: 2Gi  
	limits:  
		cpu: 1000m                  # Tells Kubernetes to set CPU limits
		memory: 2Gi                  
args:  
	- "-XX:ActiveProcessorCount=4"  # Tells JVM to "see" 4 cores, enabling multi-
                                    # threading
	- "-XX:MaxRAMPercentage=75"  
	- "-XX:+UseZGC"                 # Forces ZGC garbage collector
	- "-XX:+ZGenerational"  
	- "-jar"  
	- "/app/jvm-stats.jar"
```
