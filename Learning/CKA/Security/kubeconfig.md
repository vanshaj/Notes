1. Usually whenever we try to access resources using kubectl we can do as follow
``kubectl get pods --server my-server:6443 --client-key admin.key --client-certificate admin.crt --certificate-authority ca.cert``
2. But we don't want to keep doing this again and again , so we move our details to a kubeconfig file and use it
``kubectl get pods --kubeconfig config``
3. Kubeconfig file has 3 sections 
	1. Clusters - Are the list of clusters that we need access to 
	2. Users - User accounts with which we have access to these clusters e.g admin user, dev user , prod user or for the AWS it could be an IAM User / Role
	3. Contexts - Is used to marry these together it defines which users will be used to access which clusters. E.g we can create a context name admin@production. That will use admin account to access production cluster
4. Mapping
	1. ``--server`` this option will go in to the clusters section
	2. ``--admin --client-certificate`` will go in to the users section
	3. Then we can create a context for that

```kubeconfig.yaml
apiVersion: v1
kind: Config
current-context: my-k-admin@my-k-playground # Default context
clusters:
- name: my-k-playground
  cluster:
	  certificate-authority: ca.crt
	  server: https://my-kube-pg:6443
contexts:
- name: my-k-admin@my-k-playground
  context:
	  cluster: my-k-playground
	  user: my-kube-admin
users:
- name: my-kube-admin
  user:
   client-certificate: admin.crt
   client-key:  admin.key
```

Now there could be a scenario where we have multiple contexts in our kubeconfig file, how will the kubectl know to which context we need to 

1. View kubeconfig file ``kubectl config view``
2. Update the context to use other context ``kubectl config use-context mynew@context``


Rather than using *certificate-authority* field we can use *certificate-authority-data* field to directly add the base64 encoded content of the certificate. 

