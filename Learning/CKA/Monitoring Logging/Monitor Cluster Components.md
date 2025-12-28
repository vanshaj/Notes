1. We can monitor resources in the cluster
2. We can check how many nodes are available in the cluster and check how many of them are healthy 
3. Apart from that we can validate performance metrics for nodes how much memory/cpu/disk space/network is available for each node
4. Same we can do with pods also, we can see how many pods are available and also performance metrics for those pods
5. We have opensource third party solutions for these
	1. Metrics Server
	2. Prometheus
6. We can have one metrics server per kubernetes server
7. So metrics server gets each of the metrics from kubernetes nodes and pods and store them in it's memory
8. Metrics server is only an in-memory solution and it doesnot store data on the disk.
9. Thus we can't see historical data using Metrics-Server
10. How the metrics is exposed?
	1. Kubelet is a component that is running on each slave node of the cluster
	2. Kubelet is responsible for getting the commands from kube-apiserver running on master node and based on that performing actions of resources(like scheduling of pods) on that node
	3. Kubelet also has an agent cAdvisor continuouly running. 
	4. cAdvisor aggregates the pod performance metrics and expose that to metrics-server via kubelet api
11. Deployment
	1. ```
	   git clone https://github.com/kubernetes-sigs/metrics-server.git
	   kubectl create -f deploy/1.8+/
	   ```
	   2. This will install the deployment
12. Once kube-metrics api is done, cluster performance metrics can be viewed by running commands like ```
```
kubectl top nodes
kubectl top pods -nabc
```


## Logging
1. How to check logs of a specific container if there are mulitple containers running inside a pod . In below command we have to add name of the container at the end to get logs of a specific container from the pod
``kubectl logs -f pod-name -n namespace container-name``
2. 