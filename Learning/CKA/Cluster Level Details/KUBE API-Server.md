![[Pasted image 20251216184119.png]]
1. Whenever we run ``kubectl`` command, it actually reaches the kube-api  server , then  
	1. the kube-api server authenticates and validates the request. 
	2. then it validates the data from the etcd cluster
	3. then it returns the response
2. We don't even need kubectl cli tool we can use  POST  API request to get the data or perform any action on the cluster
	1. ```curl -X POST api-server-url/api/v1/namespaces/default/pods```
3. Now this information gets updated in the etcD cluster that a new  pod is asked to be created
4. Kube-scheduler  sees that there is a new pod with no Nodes assigned
5.  Now  Kube-scheduler identifies the Node and schedules the pod, it then returns the info back to the Kube-Api Server
6.  Kube-api Server updates that info in the etcD cluster and sends that  info back to  the WORKER NODE
7. Kubelet then creates a pod on the worker node and asks the container runtime to run the application image
8. Now kubelet updates the info back to the Kube-api server and kube-api server update this info back to the etcD cluster
9. Kube-Api Server is the  only component that directly interacts with the etcD cluster
10. ``kubectl get pods -nkube-system`` 