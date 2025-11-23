#vpa #autoscaling #k8s #chatgpt #historical

```table-of-contents
```
## Opt-in

VPA operates on an **opt-in** basis.  
You must create a `VerticalPodAutoscaler` custom resource (CR) that targets a specific Deployment, StatefulSet, or other controller — usually by its label selector, like this:

``` YAML
apiVersion: autoscaling.k8s.io/v1 
kind: VerticalPodAutoscaler 
metadata:   
	name: myapp-vpa   
	namespace: default 
spec:   
	targetRef:     
		apiVersion: "apps/v1"     
		kind: Deployment     
		name: myapp   
	updatePolicy:     
		updateMode: "Auto"
```

Until such a CR exists, VPA won’t touch or even analyze that workload.

## Historical data

**Dave:** How many days of data does Vertical Pod Autoscaler use to generate a recommendation?

**ChatGPT:** The Vertical Pod Autoscaler uses data from the last **2 days** (48 hours) to generate its recommendation.

