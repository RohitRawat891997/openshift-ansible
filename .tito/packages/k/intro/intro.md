## 🚀 Kubernetes Basics – Beginner-Friendly Explanation

-- I’m currently revising Kubernetes fundamentals, and here’s a simple and clear overview of how Kubernetes works internally 👇

## 🔹 What is Kubernetes?

Kubernetes is an open-source container orchestration tool used to deploy, manage, scale, and self-heal containerized applications automatically.

## 🏗️ Kubernetes Architecture (High Level)

Kubernetes follows a Control Plane (Master Node) + Worker Nodes architecture.

### 🧠 Control Plane (Master Node)

Responsible for cluster management and decision-making.

### 🔹 kube-api-server

Entry point of the cluster

Handles Validation, Authentication & Authorization

### 🔹 kube-scheduler

Decides which worker node will run a pod

#### Types of Pod Scheduling:
 1. Default Pod Scheduling
 2. Custom Pod Scheduling:
 1. nodeName
 2. nodeSelector
 3. Affinity / Anti-Affinity
 4. Taints & Tolerations

#### 🔹 kube-controller-manager

Ensures Desired State = Current State
if they fail inform to kube-api

####  🔹 etcd

Distributed key-value database
Stores the entire cluster state

### ⚙️ Worker Nodes

Worker nodes are where applications actually run.

####  🔹 kubelet

Communicates with the kube-api-server
Ensures pods are running as expected. 

#### 🔹 Container Runtime

Runtime containers inside pods
Examples: containerd, CRI-O, Docker

#### 🔹 kube-proxy

Handles pod-to-pod communication
Manages service networking

#### 📦 Pod

1. Smallest deployable unit in Kubernetes
2. Runs one or more containers
3. Always runs on worker nodes

#### 🔁 Simple Flow (Easy to Remember)

kubectl → API Server → Scheduler → Kubelet → Container Runtime → kube-proxy
