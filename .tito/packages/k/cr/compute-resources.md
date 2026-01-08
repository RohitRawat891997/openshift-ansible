**Compute resources:** CPU + Memory (RAM) that a Pod / container needs to run. there are two type of compute resources.
 #### 1. deployment stage/level.
 #### 2. namespace stage/level.

### Kubernetes manages these resources using:

Requests → It's possible for a container to use more resource. container can try to use more CPU,RAM
Limits → It is a hard limit the kernel enforces. Containers may not use more CPU than is specified in their cpu,memory limit. if we don't specify any request, limit you specified and uses it as the requested value for the resource.


1. **Deployment Level Compute Resources:**
```
apiVersion: apps/v1
kind: Deployment
metadata:
 name: web-app
spec:
 replicas: 2
 template:
 spec:
 containers:
 - name: nginx
 image: nginx
 resources:
 requests: # minimum guaranteed
 cpu: "200m"
 memory: "256Mi"
 limits: # maximum allowed 
 cpu: "500m"
 memory: "512Mi"
```


2. **Namespace Level Compute Resources:**
```
apiVersion: v1
kind: ResourceQuota
metadata:
 name: ns-quota
 namespace: dev
spec:
 hard:
 requests.cpu: "1"
 requests.memory: 2Gi
 limits.cpu: "2"
 limits.memory: 4Gi
```



2. **Resource quota:** ResourceQuota is a Kubernetes object that limits how many resources a namespace can use. It is mainly used in multi-team / multi-project clusters to avoid one team consuming all resources.

Example: (CPU,Memory & Count-based)
```
apiVersion: v1
kind: ResourceQuota
metadata:
 name: ns-quota
 namespace: dev
spec:
 hard:
 count/pods: "5"
 count/persistentvolumeclaims: "5"
 count/services: "2"
 count/secrets: "5"

 requests.cpu: "1"
 requests.memory: 2Gi
 limits.cpu: "2"
 limits.memory: 4Gi
```


3. **Limit range:** LimitRange is a Kubernetes object that sets default, minimum, and maximum CPU & memory values for Pods or Containers inside a namespace.

```
apiVersion: v1
kind: LimitRange
metadata:
 name: default-limits
 namespace: dev
spec:
 limits:
 - type: Container
 default:
 cpu: "500m"
 memory: "512Mi"
 defaultRequest:
 cpu: "200m"
 memory: "256Mi"
 min:
 cpu: "100m"
 memory: "128Mi"
 max:
 cpu: "1"
 memory: "1Gi"
```
