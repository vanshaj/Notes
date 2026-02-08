1. Whatever operation we do till now in the cluster, they are being done via the kube-api server
2. We can access the API server at the master nodes address and port 6443
	1. e.g we want to check the kube-apiserver version  ``curl https://kube-master:6443/version``
	2. e.g get the pods list from the API server ``curl https://kube-master:6443/api/v1/pods``
3. Kubernetes Api has multiple sub groups
	1. /healthz, /version, /api, /apis, /metrics, /logs
4. Core Group - /api , Named Group - /apis
5. Core Group - 
	1. Where all core functionalities are present ![[Pasted image 20260201203125.png]]
6. Named Groups - Are more organized and all new features are going to be available made via the named groups![[Pasted image 20260201203356.png]]
7. We can't directly acess the kube-apiserver we have to share the certificates of our credentials -k --key, --cert, --cacert
8. 