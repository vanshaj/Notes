1. When we want to manually schedule a pod on a node we can directly give the node name ``nodeName``
```
apiVersion: v1
kind: Pod
metadata:
	name: myPod
	namespace: default
	labels:
		app: my-app
spec:
	nodeName: node02
	containers:
	- name: nginx-container
	  image: nginx:latest
	  imagePullPolicy: Always
```
2. So kubernetes go through all the pods that are not having this property and those who don't have this are the candidates for the scheduler.
3. Then Scheduler pick those pods looks for a best Node for that pod, *if node is not assigned to the pod then pod will be in **Pending** state* 
4. nodeName property can't be modified once the pod is created

There is an alternate way to do this i.e with the help of binding object
We can create json for the below YAML of binding object
```
apiVersion: v1
kind: Binding
metadata:
	name: nginx
target:
	apiVersion: v1
	kind: Node
	name: node02
```
Now we have to send this Binding object to the Kube API Server for the pod which we want to schedule on this node
```
curl --header "Content-Type: application/json" --request POST --data '{JSON_EQUIVALENT_FOR_YAML}' https://${SERVER}/api/v1/namespaces/default/pods/${POD_NAME}/binding
```
