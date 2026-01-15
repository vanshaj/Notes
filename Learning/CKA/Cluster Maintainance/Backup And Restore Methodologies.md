1. What are backup candidates
	1. Resource Configuration
	2. etcd information
	3. Persistent Volumes
2. Etcd Cluster - 
	1. Store info about the state of the cluster.
	2. Rather than backing up individual resources , we can backup the whole etcd cluster
	3. During etcd configuration we mention the path where all the configuration is stored![[Pasted image 20260115163841.png]] 
	4. So we can backup the --data-dir 
	5. We can take  a snapshot of etcd cluster ``etcdctl snapshot save snapshot.db --endpoints="" --cacert="" --cert="" --key=""``
	6.  Restore the etcdserver from this backup
		1. Stop the kubeapi server ``systemctl stop kube-apiserver``
		2. Run command ``etcdctl snapshot restore snapshot.db --data-dir /var/lib/etcd-from-backup --endpoints="" --cacert="" --cert="" --key=""``
		3. Now we can configure etcdserver to use the new data directory![[Pasted image 20260115164200.png]]
		4. ``systemctl daemon-reload``
		5. ``systemctl restart etcd``
		6. ``systemctl start kube-apiserver``
	