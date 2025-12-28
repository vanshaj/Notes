kubelet -> **KUBE-API-SERVER** -> Create Pod
				|
				^
	**Authetication** is being done by Kube-API-Server via validating certificates 
	**Authorization** is being done by Kube-API-Server via validating Roles

Kubeconfig file that is being sent by the kubectl cli tool has the necessary certificates and **KUBE-API-SERVER** validates those certs

After Authentication the request goes via **Authorization** phase to check whether the user has the given necessary permission to perform the action which it's asking for
```developerRole.yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
	name: Developer
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["list", "get", "create", "update", "delete"]
```
User is assigned this role then he is able to get/list/delete/create/update pod only

The Roles are at Kubernetes API level i.e Kubernetes Resources level. But what if we want to do more? e.g 
1. *What if we don't want to create images from public docker hub registry*
2. *Don't   use latest tags for images*
3. *Don't allow container to runAsUser: root*
4. *Allow only certain capabiliities in our containers*

We can't achieve the above things with the RBAC , that's where admission controllers come into picture
Admission controller can also change the request before the pod gets created itself![[Pasted image 20251225125013.png]]

### Commands
1. Get list of enabled admission controllers by default 
``kube-apiserver -h | grep enable-admission-plugins``
2. If we are running in kubeadm based setup then run command in the pod
``kubectl exec kube-apiserver-controlplane -n kube-system -- kube-apiserver -h | grep enabled admission-plugins``
3. Enable ![[Pasted image 20251225125352.png]]
4. Enable can also be done by making change in file ``vi /etc/kubernetes/manifests/kube-apiserver.yaml``
5. Same file can be used to disable admission plugins like DefaultStorageClass by using flag ``--disable-admission-plugins=DefaultStorageClass``
6. NamespaceAutoProvision and NamespaceExists are deprecated now and are suceeded by NamespaceLifecycle admission controller
## Validating and Mutating Admission Controllers
1. Validating admission controller : e.g NamespaceExists it will check if the namespace exists and if not it will reject the request , it can't modify it just checks and accept or rejects.
2. Mutating admission controller: e.g DefaultStorageClass when we submit a request to create a PVC , our plugin will watch the request to create a PVC and check if it has storage class mentioned in it or not , if not it will modify the request to attached Default Storage Class in our Cluster even though not attached . That mean mutating admission controller can modify the request  and update it
3. Generally mutating are invoked first and then validating one's are invoked so that any change made by the mutating one are considered before going through any validation

### Custom Admission Controllers
For this we have 2 special admission controllers: 1. Mutating Admission Webhook 2. Validating Admission Webhook
We can configure these webhook to point to the server that is hosted inside the k8s cluster or outside it and our server has our own admission webhook service running with our own code and logic
After the request goes through all the built in admission controllers, it hits the webhook that's configured on our Server
Once it hits the webhook it makes a call to the admission webhook server by passing an admission review object in JSON format. This format has all the details about the request such as the user who makes the request , the kind of request that's being made and on what object and details .
On receiving the request the admission webhook server will respond with the admission review object with a result containing whether the request is allowed or not


Steps: 
1. Deploying webhook server![[Pasted image 20251225130532.png]]
2. Create webhook admission object![[Pasted image 20251225130740.png]]
	1. We don't want this to get triggered for all calls so we have added rules for which this validatingWebhookConfiguration will be triggered
