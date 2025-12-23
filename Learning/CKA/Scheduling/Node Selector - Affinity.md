### Node Selector
1. We want a pod to get scheduled on specific nodes due to any reason like resource contraints
2. So we can set limitation on the pod to get scheduled on the specific node
```
apiVersion: v1
kind: Pod
metadata:
	name: nginx-pod
	labels:
		app: my-app
spec:
	nodeSelector:
		size: Large
	containers:
	- name: nginx-con
	  image: nginx:latest
```
Node Selector ``size: Large`` is the label assigned to the node
3. Node Labels ``kubectl label nodes node-name <label-key>=<label-value>
4. Limitation - when we want to use below conditions
	1. e.f If we want to schedule pod on the node which has size Large or Medium
	2. Place pod on any node that is not small
### Node Affinity
1. Provide advance capabilities to limit pod placement on specific nodes
```
apiVersion: v1
kind: Pod
metadata:
	name: my-pod
spec:
	containers:
	- image: nginx
	  name: nginx-con
	affinity:
		nodeAffinity:
			requiredDuringScehdulingIgnoredDuringExecution:
				nodeSelectorTerms:
				- matchExpressions:
				  - key: size
				    operator: In
				    values:
				    - Large
				    - Medium
```
2. Operators ``In, NotIn, Exists``
3. nodeAffinity Type
	1. requiredDuringSchedulingIgnoredDuringExecution
		1. Place the pod only on the matching node if not found don't place it anywhere
		2. After pod is created if there is change in the label of the nodes what should happen as the value is set to Ignore During execution , no change of pod will happen
	2. preferredDuringSchedulingIgnoreDuringExecution
		1. Try to place the pod on the matching node but if not found place it anywhere
		2. After pod is created if there is change in the label of the nodes what should happen as the value is set to Ignore During execution , no change of pod will happen
4. During scheduling is the time when the pod does not exists and is created for the first time