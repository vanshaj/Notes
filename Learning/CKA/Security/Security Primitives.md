1. Kube-api server is the central of whole kubernetes
2. We communicate with the kube-apiserver via kubectl or directly access the APIs
3. Our job is to control the access to the kube-apiserver itself
4. We need to be careful about 2 things
	1. Who can access the cluster?
		1. Authentication
			1. *Certificates* for users
			2. *Service account* for machines
	2. What can they do with that cluster?
		1. Authorization
			1. *RBAC*
			2. *Attribute Based Access Control*
5. All communication between different components to the kube-apiserver is encrypted by TLS certificates![[Pasted image 20260116185133.png]]
6. What about the communication between different applications within the cluster
	1. By default all pods can access all other pods in the cluster
	2. We can restrict access between them using netpol i.e *Network Policies*