*These relies on metrics server*
### Horizontal Pod Scaling 
1. Horizontal scaling means, we add more servers rather than increasing the resources of a single server. We increase servers rather than the resources
2. In Kubernetes sense, horizontal scaling means increasing the number of pods for the given set of task
3. Manual way for workloads - we can run ``kubectl scale --replicas=<more> deploy/dep-name -n ns``
4. Automated way *Horizontal Pod Autoscaler*

### Vertical Pod Scaling
1. Vertical scaling means , we add more resources to our servers. We increase CPU and memory of our servers so that it can handle load
2. In Kubernetes sense, vertical scaling means increasing the number of resources CPU and Memory given to that specific pod
3. Manual way for workload - we can edit the deployment and change the resources - requests and limits
4. Automated way *Vertical Pod Autoscaler*

### Horizontal Pod Autoscaler
We will scale the pods horizontally when the resources limits is reached for that pod. If we have a pod that have CPU resource limits set to "450m", as soon as ``kubectl top pod`` shows us that the pod has reached resource limit we can run ``kubectl scale --replicas=3``. This is manual way
Let's see automated way
HPA continuously monitors the metrics from metrics server , and based on that it adds the pods and if everything is normal it can reduce the pods as well. It can track different set of metrics

#### Command
Below command will work on cpu threshold of 50% and will create min pods of 1 and max pods of 10. It will read the limits of CPU set on the deployment, let's assume the value is "500m". Then it continuously poll  the metrics server for usage, if the usage goes beyond 50% it modifies the number of replicas to go up/down as per usage
``kubectl autoscale deployment my-app --cpu-percent=50 --min=1 --max=10``

Get the HPA
``kubectl get hpa``

```
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
	name: my-app-hpa
spec:
	scaleTargetRef:
		apiVersion: apps/v1
		kind: Deployment
		name: my-app
	minReplicas: 1
	maxReplicas: 10
	metrics:
	- type: Resource
	  resource:
		  name: cpu
		  target:
			  type: Utilization
			  averageUtilization: 50
		  
```

### Vertical Pod Autoscaler
We will scale the pods vertically when the resources limits is reached for that pod. If we have a pod that have CPU resource limits set to "450m", as soon as ``kubectl top pod`` shows us that the pod has reached resource limit we can run ``kubectl edit deployment`` and update the resource requests and limits . It will kill the existing pod and create a new pod with new resource requests and limits
VPA doesnot comes built-in in the Kubernetes cluster. We must deploy it separately

```
git clone https://github.com/kubernetes/autoscaler.git
cd autoscaler/vertical-pod-autoscaler/
./hack/vpa-up.sh
kubectl get pods -n kube-system | grep vpa
```

#### Components
1. VPA Recommender - is responsible for continuous monitoring of resource usage from the metrics API server. Collects historical and live usage data. It doesnot modifies the pod directly just recommends
2. VPA Updater - It's role is to get data from the Recommender and if the pod doesnot match the usage and needs to be updated. It evicts them
3. VPA Admission Controller - This intervenes the pod creation process and uses the recommendation from the recommender to mutate the pod spec and apply the recommended spec values at startup . This ensures the newly created pod will start with the correct resource requests

#### VPA configuration
```
apiVersion: autoscaling.k8s.io/v1
kind: VerticalPodAutoscaler
metadata:
  name: my-deployment-vpa
spec:
  targetRef:
    apiVersion: "apps/v1"
    kind: Deployment
    name: vpa-demo-deployment
  resourcePolicy: // What is monitored
    containerPolicies:
      - containerName: '*'
        controlledResources:
          - cpu
          - memory
        maxAllowed:
          cpu: 1
          memory: 500Mi
        minAllowed:
          cpu: 100m
          memory: 50Mi
  updatePolicy:
    updateMode: "Auto"
```

Update Policy Mode has 4 modes
1. Off - Only recommends, doesnot change anything
2. Initial - Only changes pod on creation , doesnot change later
3. Recreate - Evicts pod if usage goes beyond range
4. Auto  - Update existing pods to recommended numbers. It behaves like a Recreate but if there is support for "In Place Update(where existing pods are not evicted)", this is preferred