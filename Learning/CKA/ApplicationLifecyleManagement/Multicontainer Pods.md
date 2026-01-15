1. We can have 2 containers in the same pod, communicating with each other.
2. That is why we have multicontainer pods that share the same lifecycle. 
	1. They are created and destroyed together 
	2. they share the same network space
	3. They have access to same volumes (storage)

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
	  command: ["sleep"]
	  args: ["10"]
	- name: tomcat-container
	  image: tomcat:latest
```

### Design Pattern
1. Co located containers - Containers that can communicate with each other throughout the life cycle of the application. No ability to identify which container is started first
2. Init containers - These are if we want to perform some initialisation steps before the pod is started. First the init container completes it's job , once it's completed only then the actual container in pod is started
3.  Sidecar containers - These are like init containers, they are started before the actual container is started and after this is started the actual container is started. The only difference is unlike init container which stops once the job is complete, sidecar container keeps on running throughout the lifecycle of the application. e.g Logging, Monitoring applications

### Init Container
```
apiVersion: v1
kind: Pod
metadata:
	name: myPod
	namespace: default
	labels:
		app: my-app
spec:
	initContainers:
	- name: db-checker
	  image: busy-box:latest
	  command: 'wait-for-db-to-start.sh'
	- name: api-checker
	  image: busy-box:latest
	  command: 'wait-for-api-to-start.sh'
	containers:
	- name: nginx-container
	  image: nginx:latest
	  imagePullPolicy: Always
	  command: ["sleep"]
	  args: ["10"]
```

 Order of init container is preserved, i.e first db-checker will run then api-checker will run

### Sidecar Container
```
apiVersion: v1
kind: Pod
metadata:
	name: myPod
	namespace: default
	labels:
		app: my-app
spec:
	initContainers:
	- name: db-checker
	  image: busy-box:latest
	  command: 'wait-for-db-to-start.sh'
	  restartPolicy: Always
	containers:
	- name: nginx-container
	  image: nginx:latest
	  imagePullPolicy: Always
	  command: ["sleep"]
	  args: ["10"]
```