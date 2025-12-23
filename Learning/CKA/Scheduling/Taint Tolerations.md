1. Taint and toleration doesnot tell pod to schedule on which node it only tell node to accept pods which are tolerant to the taint. 
2. the pod which is tolerant to the taint can get scheduled on any other node as well but the node will never accept pod missing the tolerations 
3. Taint applied on Node will decide which pod will get scheduled on that node
	1. This is based on the toleration of the pod
4. They are used to set restriction on what pods can be scheduled on the nodes
5. If we apply a taint to the node
	1. No pod will get scheduled until unless some toleration to that taint is explicitly mentioned in the pods
	2. As soon as we apply toleration to the pod , then there is a possibility that node can accept this pod now
6. Command to do the same 
	``kubectl taint nodes node-name key=value:NoSchedule``
7. Taint effect are of 3 types
	1. NoSchedule - Pods will not be scheduled if they dont have toleration to this taint
	2. PreferNoSchedule - System will try to avoid pod on the node but that is not guaranteed
	3. NoExecute - New pods will not be scheduled on the node and new pods will be evicted if any, if they don't tolerate the taint. The pods could be created before the taint was applied to the node in this case still they will be evicted if they don't have the toleration
8. Add toleration to pod
	```
	apiVersion: v1
	kind: Pod
	metadata:
		name: myPod
		namespace: default
		labels:
			app: my-app
	spec:
		tolerations:
		- key: "app"
		  operator: "Equal"
		  value: "Blue"
		  effect: "NoSchedule"
		containers:
		- name: nginx-container
		  image: nginx:latest
		  imagePullPolicy: Always
	```
9. Remove taint from the node (Adding - at the last)
``kubectl taint nodes node-name key=value:NoSchedule-``
