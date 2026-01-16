1. There could be different users that are accessing our application
	1. Admins, Developers, Bots
2. There are mostly 2 type of users
	1. Admins & Developers - User Account
	2. Bots - Service Accounts
3. Kubernetes doesnot natively managed the users itself. It depends on some external third party to manage these 
4. So we can't list or get users in our k8s cluster .
5. However in case of service accounts , kubernetes manage that and can get data about the same

### Users
1. It is the responsibility of the kube-apiserver to authenticate the user and process the request
2. Authentication using - TLS Certs or Third party service provider connection like Kerberos and LDAP