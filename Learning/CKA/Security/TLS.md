1. TLS certificate guarantees the communication happening between 2 parties is secure
2. With the help of certificate the client is sure that the server is actually the person who it says it is.
	1. e.g If client is communicating with the xyz.com and the xyz.com website has a valid certificate the client will then be sure that okk I am communicating with the correct site

### Assymetric Encryption
1. In this we use a pair of keys, a public key and a private key
2. There can be 2 use cases for this
3. Secure sharing
	1. In this case user A has it's private key and public key
	2. User A share it's public key to user B
	3. When user B want to share some info securely to user A he will encrypt the data using user A's public key and send the encrypted data
	4. User A will then use it's private key to decrypt the data and get the secure message
4. Integrity validation
	1. In this case user A has it's private key and public key
	2. User A will get the data from user B that needs the stamp from user A that this data is being validated by user A and is correct.
	3. So User A will get user B's data and sign it using it's private key. So it is a sort of a signature applied on user B's data by user A
	4. Then this signed data is sent to any client which want to communicate with user B
	5. This client will have user A's public key, so when he received the data from user B , he will apply public key on this data and if he is able to decrypt then that means the signature is correct

Commands to generate public and private key
```
openssl genrsa -out my-bank.key 1024
openssl genrsa -in my-bank.key -pubout > bankpub.pem
```

The integrity validation is being used in the communication with browsers and servers to protect MITM attack
1. So the client needs the public key of the server to encrypt the symmetric key and share to the server so that the later communication can be encrypted 
2. Server will send the public key to the client and even if there is any hacker he will also get the public key of server
3. Client will encrypt the symmetric key using the public key of the server , which will be received by both hacker and the server.
4. But only server will be able to decrypt it as it has it's private key to decrypt the data but hacker will not be able to do anything
5. But there is a scenario , hacker can share it's own public key to the client saying I am the server. 
6. In order to protect this, public key is never shared directly it is shared with a certificate
7. Certificate contains public key and other info of the server also signed by the private key of the CA(Certificate authority)
8. CA are some central authorities whose responsibility is to maintain the integrity of servers among the whole world. Their public keys are present in almost all browsers (clients) 
9. So when hacker shares it's public key with certificate mentioning I am the server, it will not be signed by the CA.
10. So client (in this example let's assume browser) will check and say no this is not correct and reject the communication
11. But if the server will send it's public key along with the certificate signed(encrypted using private key of the CA) by the CA, the client will validate this using public key of the CA in the browser and say yes he is the server![[Pasted image 20260116193809.png]]
12. Later uses this public key to encrypt the symmetric key and share the encrypted data to the server

Naming convention
1. Public Key
	1. Server.crt
	2. server.pem
	3. Client.crt
	4. client.pem
2. Private Key
	1. server.key
	2. server-key.pem
	3. client.key
	4. client.key.pem
### TLS in Kubernetes
1. Kubernetes consists of master and server nodes and we know the communication between these must be secured and encrypted
2. So now if admin wants to communicate with the master node's kube-apiserver via kubectl or directly via api, he needs certificates to establish his authenticity
3. So the pre-requisite is, each server must have server certificates and client must have client certificates to verify who they are
#### Server Components
1. kube-apiserver we know exposes a HTTPS service so that other kubernetes service can communicate with it and other external users as well.
		1. Hence we need to generate server certificate for the kube-apiserver
		2. e.g we generated apiserver.crt(certificate containing public key) apiserver.key(private key)
2.  etcd server stores all the configuration of a kubernetes cluster, so we need to generate server certs for this as well
	1. we generate etcdserver.crt(certificate containing public key) etcdserver.key(private key)
3.  kubelet server also exposes and HTTPs service on the worker nodes that the kube-apiserver talks to interact with the worker nodes
	1. we generate kubelet.crt(certificate containing public key) kubelet.key(private key)

#### Client Components
1. Admin users who will communicate with the kube-apiserver. Needs admin.crt and admin.key to authenticate to the kube-apiserver
2. Kube-scheduler also communicates with the kube-apiserver to look for pods that require scheduling and then lets kube-apiserver do the scheduling. So scheduler needs scheduler.crt and scheduler.key to authenticate.
3. Kube-controllermanager  also needs certificates to authenticate itself to the kube-apiserver. So needs controller.crt and controller.key
4. Kube-proxy also needs certificates to authenticate to the kube-apiserver. So needs kube-proxy.crt kube-proxy.key
5. Kube-apiserver also is the only component that communicates with the etcd server and kubelet on the worker nodes. So for that it needs it's own client crt and key. It can use the same key it use for serving apiserver.crt and apiserver.key
![[Pasted image 20260116200916.png]]
![[Pasted image 20260116200932.png]]

6. We also need a CA to sign all these certificates
7. For that we need ca.crt and ca.key

### Commands to generate certificates for the cluster
#### CA certificate generation
1. Generate keys
``openssl genrsa -out ca.key 2048``
2. Generate Certificate Signing Request
``openssl req -new -key ca.key -sub "/CN=KUBERNETES-CA" -out ca.csr``
3. Self Sign certificate
``openssl x509 -req -in ca.csr -signkey ca.key -out ca.crt``
4. Now this ca certificate is used to sign the other certificates
#### Admin User client certificate
1. Generate keys
``openssl genrsa -out admin.key 2048``
2. Generate Certificate Signing Request
``openssl req -new -key admin.key -sub "/CN=kube-admin/OU=system:masters" -out admin.csr``
3. Sign certificate using *CA*
``openssl x509 -req -in admin.csr -CA ca.crt -CAkey ca.key -out admin.crt``
4. Now this is a valid certificate for admin user in our cluster
5. Certificate is the validated UserId and key is the password for the admin user inside the cluster
6. But how do we differentiate this admin user from the other basic users
	1. we do that by adding the group details for the user in the certificate.
	2. In this case a group name *System Masters* exist on the Kubernetes with the administrative priviliges
	3. We can do this by adding group details in the OU paramaeter in our CSR request

Now we will repeat above steps with *scheduler* , *controller-manager*, *kube-proxy*

#### Usage of the admin certificate
1. We can use this to make API calls to the kube-apiserver
``curl https://kube-apiserver:6443/api/v1/pods --key admin.key --cert admin.crt --cacert ca.crt``
2. Other option is to move all these params to a config file called kubeconfig.yaml
3. Question arises why we need to mention the --cacert.
	1. Remember when our browser is communicating with the server, it has different CAs already added in the software
	2. When it tries to validate the certificate of server, server mentions that it's certificate is signed by X CA. 
	3. Hence we need to share this info that our cert is signed by this CA, go and validate it.

#### Server side certificates
#### etcd Server
1. This can be deployed as a cluster across multiple servers as in high availabililty environment
2. In that case to secure communication between different members in the cluster we must generate additional peer certs
3. Once these are generated we must specify them during the start of etcd server![[Pasted image 20260116202910.png]]

#### kube-apiserver
1. This is refered by different namej
	1. kubernetes.default.svc.cluster.local
	2. kubernetes.default
	3. IP of the pod running kube-api server
2.  Hence all or any other names which is used to refer the kube-api server shall be mentioned as CN in the certificate of the server. As only then valid connection can be established
3. Command and config file to generate csr
```
openssl req -new -key apiserver.key -subj "/CN=kube-apiserver" -out apiserver.csr -config openssl.cnf

# openssl.cnf
[req]
req_extensions = v3_req
distinguished_name = req_distinguished_name
[ v3_req ]
basicConstraints = CA:False
keyUsage = nonRepudiation
subjectAltName = @alt_names
[alt_names]
DNS.1 = kubernetes
DNS.2 = kubernetes.default
DNS.3 = kubernetes.default.svc.cluster.local
DNS.4 = kubernetes.default.svc
IP.1 = 10.96.0.1
IP.2 = 172.17.0.87
```
4. Sign the cert
``openssl x509 -req -in apiserver.csr -CA ca.crt -CAkey ca.key -CAcreateserial -out apiserver.crt -extensions v3_req -extfile openssl.cnf -days 1000``
5. Where do we mention these certs. Basically inside the configuration of the kube-apiserver![[Pasted image 20260116203732.png]]
Above we are using api server certs and same server certs to communicate with the etcd server and the kubelet server. Even though while communicating with the etcd and kubelet , kube-apiserver will be working as client still we can use same server certs
#### Kubelet certs
1. Runs on each worker nodes
2. So we need key cert pair for each node on the cluster
3. But how to name the certs. These will be named after their nodes![[Pasted image 20260116203956.png]]
4. Kubelet also needs client certificate to authenticate to the kube-apiserver. These needs to be generated as well. API server needs to know which nodes are authenticated and give it right sets of permissions. They are also system components like the kube-controllermanager and kube-scheduler , hence the name starts with the *system:node:nodename* prefix
5. Nodes are added to group named *system:nodes*
6. Then these certs are added to the  config file 

