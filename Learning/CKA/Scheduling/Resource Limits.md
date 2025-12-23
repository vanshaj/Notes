1. Each node in a cluster has set of resources, CPU/Memory
2. Each pod needs a CPU and memory
3. It is the responsibility of the kubernetes-scheduler to decide which pod to get scheduled on which node
	1. The scheduler takes into consideration the CPU and memory which pod will use 
	2. Also check what is available memory and CPU in the Nodes
	3. Based on that it make the best decision where to schedule which pod
4. Resource Requests: Minimum amount of CPU/Memory requested by the container
	1. So remember , **resource requests is always of a container**
	```
	apiVersion: v1
	kind: Pod
	metadata:
		name: tomcat-pod
		labels:
			app: tomapp
	spec:
		containers:
		- image: tomcat:latest
		  name: tomcat-con
		  ports:
		  - containerPort: 80
		  resources:
			  requests:
				  cpu: 2
				  memory: "4Gi"
			  limits:
				  cpu: 4
				  memory: "8Gi"
	```
5. 1 count of cpu means 
	1. 1 virtual CPU in AWS cloud
	2. 1 GCP core in Google cloud
	3. 1 Azure core in Azure Cloud
	4. 1 Hyper thread
6. Memory values
	1. 1 G(Gigabyte) = 1,000,000,000 bytes
	2. 1 M(Megabyte) = 1,000,000 bytes
	3. 1 K(Kilobyte) = 1,000 bytes
	4. 1 Gi(Gibibyte) =  1,073,741,024 bytes
	5. 1 Mi(Mibibyte) = 1,048,576 bytes
	6. 1 Ki(Kibibyte) = 1,024 bytes
7. We can set resource limit usage on these pods as well using limit option, so that the container will not go as much as it can and consume all the resources of the node
8. **Limits and Requests are set for each container in the pod** so if there are multiple containers then we need to set for each one
9. Now rather than setting cpu/memory resource limit for each container , how can we set a default value . We can do that by creating a resource called limitrane which is applicable at *namespace* level. It only effects the new pods which are created after limit range is created or updated
```
apiVersion: v1
kind: LimitRange
metadata:
	name: cpu-resource-constraint
spec:
	limits:
	- default:
		  cpu: 500m
	  defaultRequest:
		  cpu: 500m
	  max:
		  cpu: "1"
	  min:
	      cpu: 100m
	  type: Container	  
```
10. Resource Quota: If we want that all the resouces created in the cluster should not consume more that X CPU and Y Memory  then we can create resource quotas at the namespace level
11. It will set the maximum limit and maximum requests of all the pods together in a cluster namespace
```
apiVersion: v1
kind: ResourceQuota
metadata:
	name: my-resource-quota
spec:
	hard:
		requests.cpu: 1
		requests.memory: "2Gi"
		limits.cpu: 2
		limits.memory: "4Gi"
```