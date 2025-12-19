1. In **spec** the template will be the pod template 
2. **replicas** will be how many pods we want to start
3. We need to mention **selector** in the replica set to make understand what pods will fall inside the replica set
	1. Why? because replica set can also manage pods that are not being created by it
4. **Deployment** roles come into picture because we want to release our latest version on the pods but not on all pods, we want to release it to some pods first and then to some other pods
	1. Rolling Updates , Undo changes, Resume changes

### YAML
```
apiVersion: apps/v1
kind: ReplicaSet
metadata:
	name: my-rp-set
	namespace: default
	labels:
		app: my-app
spec:
	replicas: 3
	selector: 
		matchLabels:
			app: my-app
	template:
		metadata:
			name: my-rp-pod
			labels:
				app: my-app
		spec:
			containers:
			- image: redis
			  name: redis-con
```


### Commands
1. Scale replicas to 6 rather than 3
	``kubectl scale replicaset --replicas=6 my-rp-set``
2. Dry run command to generate yaml
		``kubectl create deployment --image=nginx nginx --replicas=4 --dry-run=client -o yaml > nginx-pod.yaml``
3. Update image on deployment
		``kubectl set image deployment/<DEPLOYMENT_NAME> <CONTAINER_NAME>=<NEW_IMAGE>:<TAG>``
		``kubectl set image deployment nginx nginx=nginx:1.18``
	