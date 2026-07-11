# Day 50 – Kubernetes Architecture and Cluster Setup

---

## Challenge Tasks

### Task 1: Recall the Kubernetes Story
Before touching a terminal, write down from memory:

1. Why was Kubernetes created? What problem does it solve that Docker alone cannot?
   
      ```
      Docker can run containers on a single machine, but it cannot efficiently manage hundreds or thousands of containers running across multiple servers.
      Kubernetes was created to solve container orchestration challenges such as:
      * Automatic container deployment
      * Scaling applications
      * Service discovery and load balancing
      * Self-healing of failed containers
      * Rolling updates and rollbacks
      * Managing applications across multiple servers
      
      In short, Docker manages containers, while Kubernetes manages containers at scale.
      ```

3. Who created Kubernetes and what was it inspired by?
   
   ```
   Kubernetes was originally created by Google. Released as an open-source project in 2014. Inspired by Google's internal cluster management system called Borg.
   ```
5. What does the name "Kubernetes" mean?

   ```
   The word Kubernetes comes from Greek and means "Pilot". This is why Kubernetes log is a shop wheel.
   ```
 
---

### Task 2: Draw the Kubernetes Architecture
From memory, draw or describe the Kubernetes architecture. Your diagram should include:

**Control Plane (Master Node):**
- API Server — the front door to the cluster, every command goes through it
- etcd — the database that stores all cluster state
- Scheduler — decides which node a new pod should run on
- Controller Manager — watches the cluster and makes sure the desired state matches reality

**Worker Node:**
- kubelet — the agent on each node that talks to the API server and manages pods
- kube-proxy — handles networking rules so pods can communicate
- Container Runtime — the engine that actually runs containers (containerd, CRI-O)

```
                     +----------------------+
                     |      kubectl         |
                     +----------+-----------+
                                |
                                v
                     +----------------------+
                     |      API Server      |
                     +----------+-----------+
                                |
            +-------------------+-------------------+
            |                   |                   |
            v                   v                   v
      +-----------+      +-----------+      +--------------+
      | Scheduler |      | Controller|      |    etcd      |
      |           |      | Manager   |      | Cluster DB   |
      +-----------+      +-----------+      +--------------+

                       Control Plane
                               |
        -------------------------------------------------
        |                                               |
        v                                               v

+--------------------+                     +--------------------+
|     Worker Node 1  |                     |     Worker Node 2  |
|--------------------|                     |--------------------|
| kubelet            |                     | kubelet            |
| kube-proxy         |                     | kube-proxy         |
| containerd         |                     | containerd         |
| Pods               |                     | Pods               |
+--------------------+                     +--------------------+
```
  

After drawing, verify your understanding:
- What happens when you run `kubectl apply -f pod.yaml`? Trace the request through each component.
- What happens if the API server goes down?
- What happens if a worker node goes down?

```
Step 1

User executes:

kubectl apply -f pod.yaml

↓

Step 2

kubectl

↓

Sends request to

↓

API Server

↓

Step 3

API Server validates YAML.

↓

Stores desired state inside

↓

etcd

↓

Step 4

Controller Manager notices:

Desired pod exists
Actual pod does not exist

↓

Step 5

Scheduler selects the best worker node.

↓

Step 6

Selected node's kubelet receives instructions.

↓

Step 7

kubelet asks container runtime:

Pull image
Create container
Start container

↓

Step 8

Pod becomes:

Running
```

```
> What Happens if API Server Goes Down?
 * kubectl commands fail
 * New pods cannot be created
 * Scaling operations stop
 * Controllers cannot make updates
 * Existing running pods continue to run
 * The cluster becomes difficult to manage until the API Server is restored.

> What Happens if a Worker Node Goes Down?
 * kubelet stops sending heartbeats
 * Node becomes NotReady
 * Controller Manager detects failure
 * Pods on that node become unavailable
 * Scheduler recreates pods on healthy nodes
 * This is Kubernetes self-healing.

```
---

### Task 3: Install kubectl
`kubectl` is the CLI tool you will use to talk to your Kubernetes cluster.

Install it:

```
jeenicj@DESKTOP-BG3MAVI:~$ kubectl version --client
Client Version: v1.36.0
Kustomize Version: v5.8.1
```

---

### Task 4: Set Up Your Local Cluster

**Option A: kind (Kubernetes in Docker)**

```
# Create a cluster
jeenicj@DESKTOP-BG3MAVI:~$ kind create cluster --name devops-cluster
Creating cluster "devops-cluster" ...
 ✓ Ensuring node image (kindest/node:v1.36.1) 🖼
 ✓ Preparing nodes 📦
 ✓ Writing configuration 📜
 ✓ Starting control-plane 🕹️
 ✓ Installing CNI 🔌
 ✓ Installing StorageClass 💾
Set kubectl context to "kind-devops-cluster"
You can now use your cluster with:
kubectl cluster-info --context kind-devops-cluster
Thanks for using kind! 😊

# Verify
jeenicj@DESKTOP-BG3MAVI:~$ kubectl cluster-info
Kubernetes control plane is running at https://127.0.0.1:38731
CoreDNS is running at https://127.0.0.1:38731/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy
To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.

# Verify
jeenicj@DESKTOP-BG3MAVI:~$ kubectl get nodes
NAME                           STATUS   ROLES           AGE   VERSION
devops-cluster-control-plane   Ready    control-plane   74s   v1.36.1
```

---

### Task 5: Explore Your Cluster
Now that your cluster is running, explore it:

```
jeenicj@DESKTOP-BG3MAVI:~$ kubectl describe node devops-cluster-control-plane
Name:               devops-cluster-control-plane
Roles:              control-plane
Labels:             beta.kubernetes.io/arch=amd64
                    beta.kubernetes.io/os=linux
                    kubernetes.io/arch=amd64
                    kubernetes.io/hostname=devops-cluster-control-plane
                    kubernetes.io/os=linux
                    node-role.kubernetes.io/control-plane=
Annotations:        node.alpha.kubernetes.io/ttl: 0
                    volumes.kubernetes.io/controller-managed-attach-detach: true
```

# List all namespaces
kubectl get namespaces

```
jeenicj@DESKTOP-BG3MAVI:~$ kubectl get namespaces
NAME                 STATUS   AGE
default              Active   24m
kube-node-lease      Active   24m
kube-public          Active   24m
kube-system          Active   24m
local-path-storage   Active   24m
```

# See ALL pods running in the cluster (across all namespaces)
kubectl get pods -A


```
jeenicj@DESKTOP-BG3MAVI:~$ kubectl get pods -A
NAMESPACE            NAME                                                   READY   STATUS    RESTARTS      AGE
kube-system          coredns-589f44dc88-fwghd                               1/1     Running   0             25m
kube-system          coredns-589f44dc88-m4l5d                               1/1     Running   0             25m
kube-system          etcd-devops-cluster-control-plane                      1/1     Running   0             25m
kube-system          kindnet-t6n98                                          1/1     Running   0             25m
kube-system          kube-apiserver-devops-cluster-control-plane            1/1     Running   1 (10m ago)   25m
kube-system          kube-controller-manager-devops-cluster-control-plane   1/1     Running   4 (13m ago)   25m
kube-system          kube-proxy-8f6bq                                       1/1     Running   0             25m
kube-system          kube-scheduler-devops-cluster-control-plane            1/1     Running   3 (12m ago)   25m
local-path-storage   local-path-provisioner-855c7b7774-mrdx4                1/1     Running   0             25m
```

Look at the pods running in the `kube-system` namespace:

```
jeenicj@DESKTOP-BG3MAVI:~$ kubectl get pods -n kube-system
NAME                                                   READY   STATUS    RESTARTS      AGE
coredns-589f44dc88-fwghd                               1/1     Running   0             26m
coredns-589f44dc88-m4l5d                               1/1     Running   0             26m
etcd-devops-cluster-control-plane                      1/1     Running   0             26m
kindnet-t6n98                                          1/1     Running   0             26m
kube-apiserver-devops-cluster-control-plane            1/1     Running   1 (11m ago)   26m
kube-controller-manager-devops-cluster-control-plane   1/1     Running   4 (14m ago)   26m
kube-proxy-8f6bq                                       1/1     Running   0             26m
kube-scheduler-devops-cluster-control-plane            1/1     Running   3 (13m ago)   26m
```

You should see pods like `etcd`, `kube-apiserver`, `kube-scheduler`, `kube-controller-manager`, `coredns`, and `kube-proxy`. These are the architecture components you drew in Task 2 — running as pods inside the cluster.

**Verify:** Can you match each running pod in `kube-system` to a component in your architecture diagram?

```
| Pod                     | Component             |
| ----------------------- | --------------------- |
| kube-apiserver          | API Server            |
| etcd                    | Cluster Database      |
| kube-scheduler          | Scheduler             |
| kube-controller-manager | Controller Manager    |
| kube-proxy              | Networking            |
| coredns                 | DNS Service Discovery |

```

---

### Task 6: Practice Cluster Lifecycle
Build muscle memory with cluster operations:

```bash
# Delete your cluster
kind delete cluster --name devops-cluster
# (or: minikube delete)

# Recreate it
kind create cluster --name devops-cluster
# (or: minikube start)

# Verify it is back
kubectl get nodes
```

Try these useful commands:
```bash
# Check which cluster kubectl is connected to
kubectl config current-context

# List all available contexts (clusters)
kubectl config get-contexts

# See the full kubeconfig
kubectl config view
```

Write down: What is a kubeconfig? Where is it stored on your machine?

```
> kubeconfig is a configuration file used by kubectl to know:

   * Which cluster to connect to
   * API Server endpoint
   * Authentication information
   * Certificates
   * Available contexts
   * Current context
> Default location:
  Linux/macOS:
  * ~/.kube/config
  
```


---

