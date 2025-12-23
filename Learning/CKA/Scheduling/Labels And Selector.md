1. Used to group things based on different features/criteria
2. Labels are properties attached to each item
3. Selectors help us filter those items
	1. e.g if we say app=my-app we will get the data which has app=my-app
	2. It works as a filter
4. Select pods based on the labels
	``kubectl get pods --selector app=my-app``
5. Pod with label
```
apiVersion: v1
kind: Pod
metadata:
	name: myPod
	namespace: default
	labels:
		app: my-app
spec:
	containers:
	- name: nginx-container
	  image: nginx:latest
	  imagePullPolicy: Always
```
6. Annotations are used to record informatory data e.g build details, name etc
```
apiVersion: apps/v1
kind: ReplicaSet
metadata:
	name: my-rp-set
	namespace: default
	labels:
		app: my-app-rs
	annotations:
		buildversion: 1.2
spec:
	replicas: 3
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
