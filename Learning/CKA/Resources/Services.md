1. Services helps pods communicate between each other
2. Helps communication between external network
3. Whatever pods are getting created in the K8s cluster they are totally in the separate network as compared to the network in which the actual nodes are present. 
4. We have to SSH into the node first and then hit the POD IP to access the pod's data. Could be anything backend API or frontend
5. But we don't want to access it from inside the k8s node
6. We want to access this outside the cluster
7. We can use services here so that machine outside the cluster can access the service and service can redirect the request coming to the service to the respective pod

#### Types
1. Node Port![[Pasted image 20251219081929.png]]
	1. Will help us my mapping a port on the ndoe by mapping port on the pod
	2. 3 ports are involved
	3. All the names of the port are from the perspective of the service
		1. NodePort - Will be the port on which the node will listen\
			1. It has a range from 30000-32767
		2. Port - This will be the service port. 
			1. Service will also be assigned a IP inside the cluster, it's cluster IP
			2. This port is used within the cluster to access the service along with it's cluster IP
		3. Target Port - This will be the target port  of pod
			1. this is port where the specific pod is listening for the incoming traffic
2. ClusterIP ![[Pasted image 20251219083057.png]]
	1. Used when we want pod to pod communication 
	2. Let's assume we want our front-end pods to communicate with backend pods
	3. But we can't use pod ips because the pod ips will change also the pods can increase or decrease based on our set of scale / replicas in deployment
	4. So we will create service , front end pod will use the name of the service directly and communicate with service without worrying about whats happening at the back of the pods structure
	5. Names of ports here also is from the perspective of the ClusterP Serivce
		1. Port - Port where service is exposed
		2. Target Port - Port where actual pod is exposing the backend
3. LoadBalancer 
	1. Rather than configuring a load balancer manually so that the traffic coming to load balancer will be handled properly to the respective node. We can leverage cloud platfrom native load balancer
	2. We don't want to share node ips to the clients to use and access our frontend or API
	3. So we will use load balancer which will generate one URL for us and handle all the traffic to the pods for us
	4. If we use the LoadBalancer in some VM which doesnot have native load balancer support it will be almost similar to the node port service


### YAML file
#### Node PORT
```
apiVersion: v1
kind: Service
metadata:
	name: my-svc
spec:
	selector:
		app: my-app
	type: NodePort
	ports:
	- targetPort: 80
	  port: 80
	  nodePort: 30008
```

#### Cluster IP
```
apiVersion: v1
kind: Service
metadata:
	name: back-end
spec:
	selector:
		app: my-app
	type: ClusterIP
	ports:
	- targetPort: 80
	  port: 80
	  
```

#### LoadBalancer
```
apiVersion: v1
kind: Service
metadata:
	name: my-svc
spec:
	selector:
		app: my-app
	type: LoadBalancer
	ports:
	- targetPort: 80
	  port: 80
	  nodePort: 30008
```

#### Imperative
Cluster IP service will get created as it's default
``kubectl expose deployment nginx --port 80``
``k expose pods redis --port 6379 --name redis-service``

`kubectl expose pod redis --port=6379 --name redis-service --dry-run=client -o yaml`
(This will automatically use the pod's labels as selectors)

``kubectl create service clusterip redis --tcp=6379:6379 --dry-run=client -o yaml``
his will not use the pods labels as selectors, instead it will assume selectors as **app=redis.** [You cannot pass in selectors as an option.](https://github.com/kubernetes/kubernetes/issues/46191) So it does not work very well if your pod has a different label set. So generate the file and modify the selectors before creating the service

**Create a Service named nginx of type NodePort to expose pod nginx's port 80 on port 30080 on the nodes:**
`kubectl expose pod nginx --type=NodePort --port=80 --name=nginx-service --dry-run=client -o yaml > nginxsvc.yaml`

