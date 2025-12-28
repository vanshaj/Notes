1. When we set replicas to 10 we can see how the rollouts of all the 10 pods are being done using command
`` kubectl rollout status deployment/my-deployment``
2. Also I can see the history of the rollout i.e any changes being made to it
`` kubectl rollout history deploy/my-deployment``
3. Deployment Stratergy
	1. Recreate: Destroy All and create all new 
		1. Not default one
		2. As application will not be available during that time
	2. RollingUpdate: Delete one and then create new one , keep on doing this till all old one's are deleted and new one's are created
		1. Default one
		2. As application will be available during that time
4. How deployment performs upgrade under the hood
	1. When a new deployment gets created , it creates first a replicaset automatically which in turn creates the desired number of pods
	2. When we update the deployment, kubernetes creates a new replicaset under the hood and starts deploying containers there and at the same time it  starts deleting the pod from the old replicaset![[Pasted image 20251226131840.png]]
	3. We can validate the above thing by checking the replicasets , whenever deployment is updated, underlying replicasets also gets created
	4. In some situations we feel the upgrade was not well so we need to go back to the previous version , we need rollback. K8s allows us to rollback to previous version
		``kubectl rollout undo deploy my-deployment``
	

### YAML

```
apiVersion: apps/v1
kind: Deployment
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
	strategy:
		rollingUpdate:
			maxSurge: 25%
			maxUnavailable: 25%
		type: RollingUpdate
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