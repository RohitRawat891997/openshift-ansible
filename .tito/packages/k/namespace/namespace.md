## 🚀 Kubernetes Namespaces – Quick Notes

### 🔹 What are Namespaces?
Namespaces help logically isolate resources inside a single Kubernetes cluster.
Resource names must be unique within a namespace, but can repeat across namespaces.

### 🔹 Why use Namespaces?
✅ Multi-team & multi-project isolation
✅ Resource control using ResourceQuota
✅ Better organization (Dev / Staging / Prod)

### 🔹 When NOT to use them?
For small clusters or minor variations → use labels, not namespaces.

### 🔹 Default Kubernetes Namespaces
• default – for general use
• kube-system – Kubernetes core components
• kube-public – publicly readable resources
• kube-node-lease – node heartbeat tracking

### 🔹 Best Practice
❌ Avoid using default in production
✅ Create dedicated namespaces per environment

### 🔹 Useful Commands
```
kubectl get ns
kubectl create ns <namespace name>
kubectl get pods -n <namespace>
kubectl config set-context --current --namespace=<namespace>
```

#### 🔹 Namespace & DNS
````
Service DNS format:
service-name.namespace.svc.cluster.local
Allows same app names across Dev / Stage / Prod.
````

#### 🔹 Important Note
Not all resources are namespaced (e.g. Nodes, PVs, StorageClass).

📌 Namespaces = Isolation, Organization & Control in Kubernetes
