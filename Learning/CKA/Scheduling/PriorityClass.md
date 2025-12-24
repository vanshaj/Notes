1. Kubernetes run different components as pods with different priorities. No matter what those components should run always
2. Same we have different components like below running as pods
	1. Databases
	2. Critical Application
3. We need to make sure the higher priority workloads keep running as compared to lower priority workloads
4. If Higher priority workload is not getting scheduled the scheduler will kill the lower priority workload to make that happen
5. Priority classes objects are not namespace bound, once they are created they can be configured on any pod in any namespace
6. We define priorities as number ranging from 
	1. -2,147,483,648
	2. 1,000,000,000
	3. Larger number means higher priority
	4. This range is only for components that are deployed as apps not part of kubernetes system
7. There is a separate range for the priority of the kubernetes components  
	1. They always get higher priority and they go max to 2,000,000,000
``kubectl get priority class``
```
apiVersion: scheduling.k8s.io/v1
kind: PriorityClass
metadata:
	name: high-priority
value: 1000000000
description: "Priority class for mission critical pods"
```
Once the above priority class is created we can assign the priority to the pod
```
apiVersion: v1
kind: Pod
metadata:
	name: myPod
	namespace: default
	labels:
		app: my-app
spec:
	priorityClassName: high-priority
	containers:
	- name: nginx-container
	  image: nginx:latest
	  imagePullPolicy: Always
```
8. Default priority of all the pods is 0 , we can set default value by using a property ``globalDefault: true`` , can only be used in one Priority Class
9. There is one more thing , preemption policy of the PriorityClass
	1. Use Case- For example there is a pod running with low priority and a new pod comes with high priority but node doesnot have the resource to schedule the pod. What will happen?
	2. This action that scheduler should take is defined by the pre-emption policy of the PriorityClass
	3. Default Value for the ``preemptionPolicy`` is ``PreemptLowerPriority`` i.e it will kill the lower priority job and take it's place.
	4. If we want to change that we can set the value to 
		1. ``never`` that is don't remove the existing pod and wait for the resources to be empty
10. 
