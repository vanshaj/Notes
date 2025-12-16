![[Pasted image 20251216200012.png]]
1. Within a cluster , every pod can reach every other pod
2. This is accomplished by deploying a pod networking solution through the cluster,  
3. Through this network pods can communicate with each other
4. Whenever we create a kubernetes service to help communicate between pods, it didn't join the pod network. Why? Because service is not a actual thing it is just a virtual component that stores in the kubernetes memory
5. But we want service to be accessible from any node across the cluster. So here comes the kube-proxy
6. Kube-proxy is a process that runs on each node in the cluster , it's job is to look for new service and every time a new service is created it creates a proper rule to forward traffic to those services to the backend pods.
7. It creates IP table rules on each nodes on the cluster . So that the traffic heading to the IP of the service will be headed to the IP of the pod![[Pasted image 20251216200441.png]]
8. Kube Proxy is running as a daemon set in the cluster so that it will be deployed on each node on the cluster