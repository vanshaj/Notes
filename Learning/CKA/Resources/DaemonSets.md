1. Like replicaset to deploy mulitple instances of a pod but the constraint is it will run one copy of the pod on each node
2. Whenever a new node is added to the cluster , the replica of tha daemonset schedule the pod on that node and whenever a node is deleted that pod also gets deleted
3. Kube-Proxy and calico are deployed as the daemonsets in the cluster
```
apiVersion: apps/v1
kind: DaemonSet
metadata:
	name: my-rp-set
	namespace: default
	labels:
		app: my-app
spec:
	selector: 
		matchLabels:
			app: my-app
	template:
		metadata:
			name: my-rp-pod
			labels:
				app: my-app
		spec:
			containers:
			- image: redis
			  name: redis-con
```
4. How does the daemon set works how it gets scheduled on each node?
	1. It uses NodeAffinity and Default Scheduler to do this
5. 