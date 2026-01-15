1. ``kubectl get nodes`` will give us Versions
2. v1.33.0
	1. 1 major version
	2. 33 minor version (Feature and functionalities)
	3. 0 patch ( Contains bug fixes )
3. Each resource has it's own version
	1. kube-apiserver : v1.13.4
	2. Controller-manager: v1.13.4
	3. kube-scheduler: v1.13.4
	4. kubelet, kube-proxy, kubectl: v1.13.4
	5. etcd-cluster: v.3.2.18
	6. CoreDNS: v.1.1.3
4. None of the version can be ever higher than the kube-apiserver as all the other components talk to kube-apiserver
5. Controller-manager and kube-scheduler can be one version lower
6. Kubelet and kube-proxy can be 2 versions lower
7. But for kubectl it could be either same, one version higher or one version lower than the kube-apiserver
![[Pasted image 20260115154002.png]]

### Upgrade
1. So how can we decide when to upgrade the kubernetes version
2. Kubernetes support only 3 minor versions at a time so e.g today if 1.12 version of K8s is released , it will keep supporting 1.12, 1.11, 1.10. So when 1.13 will be released we need to upgrade our 1.10 as it will be un-supported after the release of 1.13
3. What is the recommended way to upgrade? Directly from 1.10 to 1.13?
	1. No
	2. Recommended way is to upgrade one minor version at a time from 1.10 to 1.11 then to 1.12 and finally 1.13

### Kubeadm way upgrade
1. So let's assume our worker and master node are on same version 1.10
2. Upgrading involves 2 steps
	1. Upgrade the master node
	2. Upgrade the worker nodes
3. Upgrade the master nodes
	1. When we do it the control plane components *kube-apiserver, controller-manaher, kube-scheduler* goes down immediately
	2. This doesnot mean that our application from the worker nodes are impacted
	3. All workloads served on worker nodes will keep on serving our users
	4. But we will not be able to access the cluster using kubectl or other APIs as the *kube-apiserver* itself is down
	5. We also can't deploy new applications or modify existing applications
	6. Also as *controller-manager* is not working if a pod part of the deployment goes down , it will not be scheduled again
4. Upgrade the worker nodes. Different strategy
	1. **Upgrade all at once** : Problem our pods will be down and users will not be able to access the application. Once upgrade is up then only we can do it. But it has donwtime assosciated.
	2.  **Upgrade one node at a time**: Upgrade first node but first draining it and then upgrading it so that users will not face any issue. Then do this with other nodes as well
	3. **Add new nodes to cluster**: This strategy will add new nodes to the cluster will new version and then remove the old nodes. Convenient if we are on cloud envs

Commands
1. First upgrade the kubernetes cluster components
```
# First upgrade kubeadm to latest version itself
apt-get upgrade -y kubeadm=1.12.0-00
# Then plan
kubeadm upgrade plan
# Then upgrade apply
kubeadm upgrade apply v1.12.0
# Upgrade kubelet on master nodes
apt-get upgrade -y kubelet=1.12.0-00
# Restart kubelet service
systemctl restart kubelet
```
2. Then upgrade the kubernetes worker nodes i.e kubelet
```
# First move all running workloads from the first worker nodes to other
kubectl drain node01
# Upgrade the kubeadm and kubelet
apt-get upgrade -y kubeadm=1.12.0-00
apt-get upgrade -y kubelet=1.12.0-00
# Now apply the config
kubeadm upgrade node config --kubelet-version v1.12.0
# Restart service
systemctl restart kubelet
# Now mark it schedulable
kubectl cordon node01
```
