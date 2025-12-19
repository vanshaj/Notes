1. This controller is a process that continuously monitors the state of various components withi the system and works together to  bring  the system in the desired state
2. **NODE CONTROLLER***  - 
	1. checks the health of  the  nodes every 5 seconds from the kubeapi server. And if it doesnot receives the health status . It will update the status of the node as unhealthy in the etcD cluster and after every 45 seconds it will keep checking
	2. **NODE CONTROLLER** Manager gives 5 mins to node to come back up. If it doesnot come back up then , node controller evicts the pods assigned on that node and assign it to some other NODE
	3. NODE Monitor period - 5 seconds, Node Monitor Grace Period - 40 seconds, Node Eviction Timeout - 5 mins
3. **REPLICATION CONTROLLER** - 
	1. Checks if the desired number of pods are always present on the cluster
	2. If a pod dies it creates a new one
4. Many more such controllers are available in the Kubernetes
	1. Deployment-Controller
	2. Namespace-Controller
	3. Endpoint-Controller
5. All concepts are implemented and handled via these controllers like service, namespace, statefulset, persistentvolumes
6. All these controllers are inside Kube-Controller-Manager
7. 