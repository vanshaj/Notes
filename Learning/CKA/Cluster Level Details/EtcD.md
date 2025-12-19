1. Simple reliable key value store (distributed)
2. Key value store is different from the Tabular database as there is only one key and all other info is stored as respect to that key
3. Document Store - 
	1. Store information in form of documents
	2. Each individual will get a different document with different entries
	3. Change in one document will not affect the other document
	4. Files can be  in any format  (json/table/yaml)
	5. Usually they store data in json format
	6. No need  of schema in document store
4. Key Value Store  -
	1. Store values  against a key
	2. e.g `Key: name, Value: John` `Key: age, Value: 45`
	3. Another ``` Key: user:john_doe
	   Value: name=john_doe, age=45, location=US```
	4. Another key normal and value is full json document```
	   Key: user:john
	   Value: '{"name": "john_doe", "age":  45}' ```
5. Install etcD
	1. Download from repo
6. Run  etcD
	1. ./etcD
7. It starts a  server that listens on  port **2379** 
8.  EtcD follows a client server architecture so the above commands starts a  server , now we can attach any client to this server
9. `etcdctl` is a cli client that we can connect to our etcd server. 
	1. Command to add the key value to the etcd Server via etcdctl
		1. ./etcdctl put key1 value1
	2. Command to get the value of a specific key from the etcD server
		1. ./etcdctl get key1
	3. Other options in v3
		1. del (delete)
		2. txn (transaction)
10. EtcD in  Kubernetes Cluster


### Role in Kubernetes Cluster
1. Every info we see when we run ``kubectl get`` command , we see  it from the etcD cluster
2. Every change we made in our cluster are made in the etcD cluster like
	1. Adding pod
	2. Updating replicase
	3. Adding nodes
3. There are 2 ways to setup  the kubernetes cluster
	1. Manual i.e we install everything from scratch manually
		1. We will fetch the etcD binary and add different configs e.g --advertise-client-urls. This is URL  where our etcD will keep on listening
		2. Using ``kubeadm`` , this will install etcD as a pod in the kube-system namespace
4. Let's assume we deployed using ``kubeadm``, now we can exec into pod and get all keys
	1. ``kubectl exec -it etcd-master -n kube-system etcdl get / --prefix -keys-only``
5. If we have a **HA** i.e High Availability  environment , we would have multiple master  nodes and  for multiple master nodes we will have different etcd instances across the master nodes. In that case we have to make sure that etcD instances know about each other by setting the  correct configuration ``--initial-cluster`` 