```
env:
- name: APP_COLOR
  value: Pink
```

```
env:
- name: APP_COLOR
  valueFrom:
	  configMapKeyRef:
```

```
env:
- name: APP_COLOR
  valueFrom:
	  secretKeyRef:
```

### Config Map
Create cmap using imperative command
app-config is name of the config-map in the below command
``kubectl create cm app-config --from-literal=APP_COLOR=blue --from-literal=APP_MOD=prod``

Let's assume we have a properties file from which we want to create a cmap
``kubectl create cm app-config --from-file=appsecret.properties``

```
apiVersion: v1
kind: ConfigMap
metadata:
	name: app-config
data:
	APP_COLOR: blue
	APP_MOD: prod
```

Now how can we inject both env variable from this configmap into our pod
```
apiVersion: v1
kind: Pod
metadata:
	name: my-app-color
spec:
	containers:
	- name: my-app-color
	  image: nginx:v1
	  ports:
	  - containerPort: 8080
	  envFrom:
	  - configMapRef:
		    name: app-config
```

### Secrets
Create secrets imperative command
``kubectl create secrets generic app-config --from-literal=APP_COLOR=blue --from-literal=APP_MOD=prod``
1. Create *generic* secret from file
``kubectl create secret generic my-seceret --from-file=folder/file1 --from-file=folder/file2 -n custom``
2. Create *tls* secret from cert and key
``kubectl create secret tls cert-secret --cert=folder/file.crt --key=folder/file.key -n custom``
3. Create secret using yaml
```
apiVersion: v1
kind: Secret
metadata:
	name: app-secret
data:
	DB_HOST: base64_encoded(host)
	DB_PASSWORD: base64_encoded(password)
	DB_USER: base64_encoded(user)
```
4. Now how can we inject both env variable from this secret into our pod
```
apiVersion: v1
kind: Pod
metadata:
	name: my-app-color
spec:
	containers:
	- name: my-app-color
	  image: nginx:v1
	  ports:
	  - containerPort: 8080
	  envFrom:
	  - secretRef:
		    name: app-secret
```

5. Note on Secrets
	1. Remember that secrets encode data in base64 format. Anyone with the base64 encoded secret can easily decode it. As such the secrets can be considered as not very safe.
	2. The concept of safety of the Secrets is a bit confusing in Kubernetes. The [kubernetes documentation](https://kubernetes.io/docs/concepts/configuration/secret) page and a lot of blogs out there refer to secrets as a "safer option" to store sensitive data. They are safer than storing in plain text as they reduce the risk of accidentally exposing passwords and other sensitive data. In my opinion it's not the secret itself that is safe, it is the practices around it. 
	3. Secrets are not encrypted, so it is not safer in that sense. However, some best practices around using secrets make it safer. As in best practices like:
	- Not checking-in secret object definition files to source code repositories.
	- [Enabling Encryption at Rest](https://kubernetes.io/docs/tasks/administer-cluster/encrypt-data/) for Secrets so they are stored encrypted in ETCD. 
6. Also the way kubernetes handles secrets. Such as:
	- A secret is only sent to a node if a pod on that node requires it.
	- Kubelet stores the secret into a tmpfs so that the secret is not written to disk storage.
	- Once the Pod that depends on the secret is deleted, kubelet will delete its local copy of the secret data as well.
7. Read about the [protections](https://kubernetes.io/docs/concepts/configuration/secret/#protections) and [risks](https://kubernetes.io/docs/concepts/configuration/secret/#risks) of using secrets [here](https://kubernetes.io/docs/concepts/configuration/secret/#risks)