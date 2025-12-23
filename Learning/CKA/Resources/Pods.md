1. Smallest object we can create in the kubernetes
2. Pod is an encapsulation of containers
3. Pod can have multiple containers, not for scaling but we need some helper container to do the supporting tasks
	1. These containers can communicate with each other referring as local host i.e helper container and actual container


### Commands
``kubectl run nginx --image nginx:latest --labels app:my-app --port 80``
``kubectl get po -n custom``

If you want to replace the existing pod with the new yaml i.e delete old one and create new one
``kubectl replace --force -f nginx.yaml``

Select pods based on the labels
``kubectl get pods --selector app=my-app``

Get image name from the pod
``kubectl get po webapp -o jsonpath='{..image}'``

### YAML
4 top level parameters ( Root level properties )
1. apiVersion
	1. is of type string
2. kind
	1. is of type string
3. metadata
	1. is of type dictionary
	2. Data about the pod e.g name(of type string) and labels(dictionary)
4. spec
	1. is of type dictionary
	2. Contains the actual info about the specification of the pod
	3. Data can contain different info e.g containers(is of type list), volumes

For pod apiVersion is v1

```
apiVersion: v1
kind: Pod
metadata:
	name: myPod
	namespace: default
	labels:
		app: my-app
spec:
	containers:
	- name: nginx-container
	  image: nginx:latest
	  imagePullPolicy: Always
```

### Commands
1. Command to create yaml directly
	1. ``kubectl run nginx --image=nginx --dry-run=client -o yaml > nginx-pod.yaml``