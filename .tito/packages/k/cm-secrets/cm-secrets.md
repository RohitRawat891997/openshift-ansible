**ConfigMap** is an API object used to store non-confidential data in key-value pairs. Pods can consume ConfigMaps as environment variables, command-line arguments, or as configuration files in a volume.

Attach configmap in two ways: 

1. variable
2. as volume mounts read-only

example1: ConfigMaps as files from a Pod (as volume mounts read-only )
```
command-line (example): kubectl create configmap my-config --from-file=/tmp/httpd.conf
```
manifest file (example):
```
apiVersion: v1
kind: ConfigMap
metadata:
 name: my-config
data:
 httpd.conf: | 
 <VirtualHost *:80>
 ServerName localhost
 DocumentRoot /var/www/html/
 <VirtualHost> 
----

apiVersion: v1
kind: Pod
metadata:
 name: mypod
spec:
 containers:
 - name: mypod
 image: nginx:latest
 volumeMounts:
 - name: foo
 mountPath: "/etc/apache2/"
 volumes:
 - name: foo
 configMap:
 name: my-config
```


example2: Configmaps as environment variables (variable type)
```
command-line (example): kubectl create configmap app-config --from-literal=APP_PORT=8080 
```
manifest file (example):
```
apiVersion: v1
kind: ConfigMap
metadata:
 name: app-config
data:
 APP_PORT: "8080"
---

apiVersion: v1
kind: Pod
metadata:
 name: env-configmap
spec:
 containers:
 - name: nginx
 image: nginx:latest 
 envFrom:
 - configMapRef:
 name: app-config 
```
```
controlplane:~$ kubectl exec -it  env-configmap -- bash
root@env-configmap:/# env | grep 8080
APP_PORT=8080
root@env-configmap:/# 
```

#### Credentials Securely Using Secrets ####

A Secret is an object that contains a small amount of sensitive data such as a password, a token, tls or private registry .

types of secret

1. generic
   1. password
   2. ssh

2. tls
3. registry

type of attach secret 
  1. env variable
  2. mount volume 


URL= https://lnkd.in/g8BAYwAc

echo -n 'myusername' | base64 -d
echo -n 'mypassword' | base64 -d


command line (example): kubectl create secret generic test-secret --from-literal=password='mypassword'

manifest example: 
```
apiVersion: v1
kind: Secret
metadata:
 name: test-secret
data:
 password: bXlwYXNzd29yZA==
---

apiVersion: v1
kind: Pod
metadata: 
 name: mypod
spec:
 containers:
 - name: mysql
 image: mysql:8.0
 env:
 - name: MYSQL_ROOT_PASSWORD
 valueFrom:
 secretKeyRef:
 name: test-secret
 key: password
```
```
 kubectl get pod
NAME  READY  STATUS  RESTARTS  AGE
mypod  1/1   Running  0     37s
controlplane:~$ 
controlplane:~$ 
controlplane:~$ kubectl exec -it mypod -- bash
bash-5.1# mysql -uroot -pmypassword
mysql: [Warning] Using a password on the command line interface can be insecure.
Welcome to the MySQL monitor. Commands end with ; or \g.
Your MySQL connection id is 8
Server version: 8.0.44 MySQL Community Server - GPL

Copyright (c) 2000, 2025, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
bash-5.1# exit
exit
controlplane:~$ 
```



#### SSH authentication Secrets ####
```
controlplane:~$ kubectl create  secret  generic  demo-secrets --from-file=/root/.ssh/id_rsa.pub --from-file=/root/.ssh/id_rsa
secret/demo-secrets created
controlplane:~$ 
controlplane:~$ 
controlplane:~$ 

apiVersion: v1
kind: Pod
metadata:
 name: mypod
spec:
 containers:
 - name: mypod
 image: redis
 volumeMounts:
 - name: foo
 mountPath: "/etc/foo"
 volumes:
 - name: foo
 secret:
 secretName: demo-secrets
 optional: true
controlplane:~$ 
```


#### TLS Secrets ####
```
kubectl create secret tls my-tls-secret --cert=cert.pem --key=cert.key 
```
```
apiVersion: v1
kind: Pod
metadata:
 name: mypod
spec:
 containers:
 - name: mypod
 image: redis
 volumeMounts:
 - name: foo
 mountPath: "/etc/foo"
 volumes:
 - name: foo
 secret:
 secretName: my-tls-secret 
 optional: true

controlplane:~$ 
controlplane:~$ kubectl  exec -it mypod -- ls /etc/foo/
tls.crt tls.key
controlplane:~$ 
controlplane:~$ 
```

#### private registry ####
```
kubectl create secret docker-registry regcred --docker-server=docker.io --docker-username=<your-username> --docker-password=<your-password>
```
```
apiVersion: v1
kind: Pod
metadata:
 name: private-reg
spec:
 containers:
 - name: private-reg-container
 image: <your-private-image>
 imagePullSecrets:
 - name: regcred
```
