1. Kubernetes is highly customizable, we can create our own custom scheduler and use it as default or along with the default kubernetes scheduler
2. While creating a pod we can specify to run or schedule a pod by the specific scheduler
3. Names must be different , for each scheduler we can create a different config file
```custom-kube-scheduler.yaml
apiVersion: kubescheduler.config.k8s.io/v1
kind: KubeSchedulerConfiguration
profiles:
- schedulerName: my-custom-sched
leaderElection:
	leaderElect: true
	resourceNamespace: kube-system
	resourceName: lock-object-my-custom-sched
```

4. Scheduler can be deployed using kube-scheduler binary
	``` wget  <binarypath>
	/usr/local/bin/kube-scheduler --config=/etc/kubernetes/config/custom-kube-scheduler.yaml
	```
5. There can be multiple above files that you can use to deploy scheduler
6. Leader elect option
	1. is used when multiple options of the scheduler is running on different master nodes
	2. When we have HA setup where we have multiple master nodes as a kube-scheduler process running on both of them
	3. If multiple copies are running at a time only one can be selected at a time , there is where leader elect option can be used
7. Use the custom scheduler in a Pod
```sched-pod.yaml
apiVersion: v1
kind: Pod
metadata:
	name: my-pod
	namespace: default
	labels:
		app: myapp
spec:
	schedulerName: my-custom-sched
	containers:
	- name: my-container
	  image: nginx:latest
	
```


Recently there was a support added to create multiple profiles for multiple schedulers using sing binary rather than creating different processes for each scheduler and configure the plugins inside the yaml for separate scheduler
```custom-multiple-kube-scheduler.yaml
apiVersion: kubescheduler.config.k8s.io/v1
kind: KubeSchedulerConfiguration
profiles:
- schedulerName: my-custom-sched
  plugins:
	  score:
		  disabled:
		  - name: TaintToleration
		  enabled:
		  - name: MyCustomPluginQA
		    name: MyCustomPluginQB
- schedulerName: my-custom-sched-1
  plugins:
	  preScore:
		  disabled:
		  - name: "*"
	  score:
		  disabled:
		  - name: "*"
- schedulerName: my-custom-sched-2
leaderElection:
	leaderElect: true
	resourceNamespace: kube-system
```
### Scheduler Profiles

There is a process through which pod goes during scheduling phase

Scheduling Queue -> Filtering -> Scoring -> Binding

1. Scheduling Queue: Based on the priority class , the pod which has high priority comes first in the scheduling queue
2. Filtering: Now pod has some resource requests, based on that which node has that sufficient resources the Nodes get filtered out. That means Nodes that doesnot have sufficient memory/cpu required by the nodes are filtered out
3. Scoring: Node which has high resources as compared to the other node once the pod is scheduled get high score and hence selected
4. Binding: Then the pod get binded to this specific node

All these operations are achieved by certain plugins
1. Scheduling Queue: PrioritySort Plugin
2. Filtering: NodeResourcesFit/NodeName/NodeUnschedulable Plugin
3. Scoring: NodeResourcesFit/ImageLocality(Node which already has that image of pod) Plugin
4. Binding: DefaultBinder Plugin

We can customize plugins which plugins go where , also we can create our own plugin and extend them . There are extensions to which these plugins get fit to and we can create our own plugin by extending those extensions
![[Pasted image 20251225123120.png]]




