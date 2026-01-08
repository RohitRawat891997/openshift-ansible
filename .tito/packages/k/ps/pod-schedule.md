## In kubernetes there are 4 types of Pod scheduling.

1. NodeName based.
2. nodeSelector based.
3. Affinity and Anti-Affinity based
4. Taint and Toleration based.


1. **nodeName:** we don't need kube-schedule component running while working with nodeName based scheduling. when it comes to production we never use this.
   
```
apiVersion: v1
kind: Pod
metadata:
 name: nginx
spec:
 nodeName: <worker node> hashtag#nodeName based type 
 containers:
 - name: nginx
 image: nginx:latest
 ```

2. **nodeSelector:** You can add the nodeSelector field to your Pod specification and specify the node labels you want the target node to have. Kubernetes only schedules the Pod onto nodes that have each of the labels you specify.

Note: there are 2 types of nodeSelector:

 1. Deployment level
 2. Namespace level

1. **Deployment level** based nodeSelector
```
apiVersion: v1
kind: Pod
metadata:
 name: nginx
spec:
 nodeSelector: hashtag#nodeSelector based type
 <node labels> 
 containers:
 - name: nginx
 image: nginx:latest
```
2. **Namespace level** based nodeSelector
   
```
---
apiVersion: v1
kind: Namespace
metadata:
 name: dev
 annotations:
 https://lnkd.in/gHm58784: team=dev

---
apiVersion: v1
kind: Pod
metadata:
 name: nginx
 namespace: dev
spec: 
 containers:
 - name: nginx
 image: nginx:latest
```
3. **Affinity and Anti-affinity:** Affinity/anti-affinity gives you more control over the selection logic. there are 2 type.
 1. nodeAffinity/nodeAntiAffinity:
 2. podAffinity/podAntiaffinity:

1. **Node Affinity:** Node affinity is conceptually similar to nodeSelector, allowing you to constrain which nodes your Pod can be scheduled on based on node labels. There are two types of node affinity:

requiredDuringSchedulingIgnoredDuringExecution: The scheduler can't schedule the Pod unless the rule is met. This functions like nodeSelector, but with a more expressive syntax.
preferredDuringSchedulingIgnoredDuringExecution: The scheduler tries to find a node that meets the rule. If a matching node is not available, the scheduler still schedules the Pod.

#### 1. *requiredDuringSchedulingIgnoredDuringExecution:*
```
apiVersion: apps/v1
kind: Deployment
metadata:
 name: nginx-deployment
 labels:
 app: nginx
spec:
 replicas: 3
 selector:
 matchLabels:
 app: nginx
 template:
 metadata:
 labels:
 app: nginx
 spec:
 affinity:
 nodeAffinity:
 requiredDuringSchedulingIgnoredDuringExecution:
 nodeSelectorTerms:
 - matchExpressions:
 - key: type
 operator: In
 values:
 - frontend
 containers:
 - name: nginx
 image: nginx:1.14.2
```

#### 2. *preferredDuringSchedulingIgnoredDuringExecution:*

**Node affinity weight:** You can specify a weight between 1 and 100 for each instance of the preferredDuringSchedulingIgnoredDuringExecution affinity type.
```
 affinity:
 nodeAffinity:
 preferredDuringSchedulingIgnoredDuringExecution:
 - weight: 1
 preference:
 matchExpressions:
 - key: type
 operator: In
 values:
 - frontend
```

2. **Pod anti-affinity** requires nodes to be consistently labeled, in other words, every node in the cluster must have an appropriate label matching topologyKey. If some or all nodes are missing the specified topologyKey label, it can lead to unintended behavior.
Similar to node affinity are two types of Pod affinity and anti-affinity as follows:

#### *requiredDuringSchedulingIgnoredDuringExecution:*
#### *preferredDuringSchedulingIgnoredDuringExecution:*
```
apiVersion: v1
kind: Pod
metadata:
 name: with-pod-affinity
spec:
 affinity:
 podAffinity:
 requiredDuringSchedulingIgnoredDuringExecution:
 - labelSelector:
 matchExpressions:
 - key: security
 operator: In
 values:
 - S1
 topologyKey: https://lnkd.in/gHkrYD5t
 podAntiAffinity:
 preferredDuringSchedulingIgnoredDuringExecution:
 - weight: 100
 podAffinityTerm:
 labelSelector:
 matchExpressions:
 - key: security
 operator: In
 values:
 - S2
 topologyKey: https://lnkd.in/gHkrYD5t
 containers:
 - name: with-pod-affinity
 image: registry.k8s.io/pause:3.8
```
4. **taint and toleration:** Taints and tolerations work together to ensure that pods are not scheduled onto inappropriate nodes. One or more taints are applied to a node; this marks that the node should not accept any pods that do not tolerate the taints.
```
kubectl taint nodes node1 key1=value1:NoSchedule
```

```
apiVersion: v1
kind: Pod
metadata:
 name: nginx
spec:
 containers:
 - name: nginx
 image: nginx:latest
 tolerations:
 - key: "key1"
 operator: "Equal" 
 value: "value1"
 effect: "NoSchedule"
```
