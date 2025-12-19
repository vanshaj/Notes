#### Command
1. Switch the namespace
	1. ``kubectl config set-context --current --namespace=mycustomns``
	2. Now all commands executed will be in above namespace rather than default
2. Get pods in all namespaces
	1. ``kubectl get pods -A``
3. 

#### YAML
```
apiVersion: v1
kind: Namespace
metadata:
	name: mycustomns
```
Limit Resources in a Cluster ,for a specific namespace we can create a resource quota
Below will limit number of pods to be created in a namespace with specific memory, cpu requests and limits
```
apiVersion: v1
kind: ResourceQuota
metadata:
	name: nsquota
	namespace: dev
spec:
	hard:
		pods: "10"
		requests.cpu: "4"
		requests.memory: 5Gi
		limits.cpu: "10"
		limits.memory: 10Gi
```
