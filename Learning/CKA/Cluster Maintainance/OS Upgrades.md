1. There could be a scenario where we have to take down node to apply patches or update the underlying OS version
2. So we have a scenario
	1. We have 2 pods running on  a node
	2. But let's assume that node goes down so what happens to the pods on these nodes
	3. If the node comes back up immediately the pods are scheduled on those nodes again and will start serving the application
	4. But if the node doesnot come up within 5 mins.  Here 5 mins is *Pod Eviction timeout* and can be updated
	5. Kubernetes consider that  pods as dead and if that pod is a part of Deployment/Replicaset , it gets scheduled on some other node
	6. So if we want to handle it carefully , there is a concept called drain node
3.  Draining node doesnot stop or kill the node, it just moves the workload running on this node to a different node.  This node is also marked as un-scheduled.  No pods can now be scheduled on this node until we specifically remove restrictions
	``kubectl drain node-1``
4. After we reboot the first node and it comes back up still no pod can be scheduled on this node. We need to un-cordon it
	``kubectl uncordon node-1``
5. There is one more command to make pod un-schedulable i.e *cordon* . It will ensure no new pods will be created on this node but will not remove the existing pods