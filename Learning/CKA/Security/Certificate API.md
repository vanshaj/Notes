1. Let's assume there comes a new user who wants to communicate securely with our cluster
2. He will generate a key pair and then generate a CSR request and sends it to us (Cluster Admin)
3. Then we will use our CA to sign the CSR with private key of CA and send the certificate back to the user
4. Now the user can use this certificate to communicate with the Cluster.
5. Each certificate has a validity period, it ends after some time (could be days, years)
6. We keep talking about CA whose private key will be used to sign the CSR of the user and generate certificate but where is this CA server located?
	1. CA is basically a public private key pair of our cluster who our cluster trusts as a valid Certificate Authority
	2. If someone gets access to the CA key pair , he can create as many users as he want and gets access to the cluster
	3. So we have to save them very securely on a server
	4. This server where we store our CA key pair becomes our CA server
	5. As per now our CA certificates are stored on the Master node, so our master node in this case is our CA server
	6. kubeadm also does the same, it generates a CA key pair and stores it on the Master Node
7. Currently we manually sign the certificates but we need an automated way so that tomorrow if our user base increase we can quickly do this activity
8. Kubernetes has Certificates API for this how does it work let's see?
	1. With the certificates API, we now send  a certificate signing request directly to kubernetes through Certificates API
	2. Now when admin receives  a CSR request , rather than manually logging in to the Master Node and signing the cert himself it will do as follows
		1. Create *CertificateSigningRequest* Object
		2. Review Requests
		3. Approve Requests
		4. Share Certs To Users 

User 
```
openssl genrsa -out user.key -b 2048
openssl req -new -key user.key -subj "/CN=username" -out user.csr
```

Now this csr will be send to admin
csr is base64 encoded and used in csr object
```
cat user.csr | base64

LS0tLS1CRUdJTiBDRVJUSUZJQ0FURSBSRVFVRVNULS0tLS0KTUlJQ1ZqQ0NBVDRDQVFBd0VURVBN
QTBHQTFVRUF3d0dZV3R6YUdGNU1JSUJJakFOQmdrcWhraUc5dzBCQVFFRgpBQU9DQVE4QU1JSUJD
Z0tDQVFFQTVJbXcvQjhxK21rUHRTMTFka2JGMkdzZ0RJVUEzUzNPNXRKTCt3R3k4bGRCCktObTE1
ZU85YktlQmIwZGNZWkNJQVkrNmFZNUwxeklhMWlzME5ZUnE1WHAva29oRU1QYk5zSmUvU0R3ZS9u
dDgKSVhQdE9ZQVRHVG4wbFNaWVY3TkJmdmdjWE5FN1c0NDMrSGwrZmtxQ2xieC9rQlNvZDhMbjV6
dWdEQzFYbFBmNwphaFVGckxGN0UyQk1PTnBQSmVYekNtS1RtL3pldkxiV0F2TWJzNHNhNysyaVlk
Q3BJT1FUMHcvYkNGa3plaGsrClhZK3VEMkFFU3Z5QWE3NnA4TDY0b0VTNHJ4Tm9COGFsLzYyLzR5
Y25sVjVVcXlTc3RSNnRlOVBPTk43NHVJcVoKbXcrbDFROVV5T0JoYXpmZDlJaWpnZGM0cmJXZTRO
SVduMnNobVZ0WlVRSURBUUFCb0FBd0RRWUpLb1pJaHZjTgpBUUVMQlFBRGdnRUJBRE1XRE5ya2tR
L0llYXJFRVVPWUJpaDN4dDBENHk3cU9lbVR6cTFzR1ZJa0ViY0FuREVWClhYSmtVREhuM2pIQ2FR
SllGK0Nnc0RSM1F0OU9SdmYweHdXc0hUTmw4UERkRVlFUXM0aldEaW9MaU80WkxwZ0IKbU1EVEI4
bndYZWx6eFpuRjBHMTA5KzFxd1FZL0dGSGtmNkJDRTVnd1JadVhDdGpWa2MveTB5MDlwYVhJZmRq
Ywp0dXgzVk9SczM1cm9QRE9OQk5ORW9pVTcvRWdPNmlEUGFVUW9ySnZWSkcvenR6N3lEZ3JDYTRK
Qlp5cEQwdXhWCmUwcURaRk1DWnhmRkJMWC9aQW92d3lrSnE2S1ZOcHFydVpYNytyZGUrUUppT2xn
ZjdoR3lZTDR0ajJLY05iTGUKdWxwZ2VqeVNmcnFjY1RXL0w1bW84TjZzM0xQS0RSc0I3bHc9Ci0t
LS0tRU5EIENFUlRJRklDQVRFIFJFUVVFU1QtLS0tLQo=
```
Admin creates CSR object
```pod-definition.yml
apiVersion: certificates.k8s.io/v1
kind: CertificateSigningRequest
metadata:
	name: user
spec:
	expirationSeconds: 600
	signerName: kubernetes.io/kube-apiserver-client
	usages:
	- any
	- server auth
	request:
	base64encoded(user.csr)
```
Once the object is created admin can see all the csr requests using
``kubectl get csr``
Then approve the request 
``kubectl certificate approve user``
Now the certificate can be extracted and shared by user as it is signed now by CA
``kubectl get csr user -o yaml``

9. Now the question arises who does all this for us (k8s components)
	1. All these are done by controller manager![[Pasted image 20260121180350.png]]
10. While configuring kube-controller-manager, there is 2 option to mention this config![[Pasted image 20260121180434.png]]
