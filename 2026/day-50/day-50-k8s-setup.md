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
```bash
# macOS
brew install kubectl

# Linux (amd64)
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
chmod +x kubectl
sudo mv kubectl /usr/local/bin/

# Windows (with chocolatey)
choco install kubernetes-cli
```

Verify:
```bash
kubectl version --client
```

---

### Task 4: Set Up Your Local Cluster
Choose **one** of the following. Both give you a fully functional Kubernetes cluster on your machine.

**Option A: kind (Kubernetes in Docker)**
```bash
# Install kind
# macOS
brew install kind

# Linux
curl -Lo ./kind https://kind.sigs.k8s.io/dl/latest/kind-linux-amd64
chmod +x ./kind
sudo mv ./kind /usr/local/bin/kind

# Create a cluster
kind create cluster --name devops-cluster

# Verify
kubectl cluster-info
kubectl get nodes
```

**Option B: minikube**
```bash
# Install minikube
# macOS
brew install minikube

# Linux
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube

# Start a cluster
minikube start

# Verify
kubectl cluster-info
kubectl get nodes
```

Write down: Which one did you choose and why?

---

### Task 5: Explore Your Cluster
Now that your cluster is running, explore it:

```bash
# See cluster info
kubectl cluster-info

# List all nodes
kubectl get nodes

# Get detailed info about your node
kubectl describe node <node-name>

# List all namespaces
kubectl get namespaces

# See ALL pods running in the cluster (across all namespaces)
kubectl get pods -A
```

Look at the pods running in the `kube-system` namespace:
```bash
kubectl get pods -n kube-system
```

You should see pods like `etcd`, `kube-apiserver`, `kube-scheduler`, `kube-controller-manager`, `coredns`, and `kube-proxy`. These are the architecture components you drew in Task 2 — running as pods inside the cluster.

**Verify:** Can you match each running pod in `kube-system` to a component in your architecture diagram?

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

---

## Hints
- kind requires Docker to be running (it creates clusters using containers)
- minikube can use Docker, VirtualBox, or other drivers
- The default kubeconfig file is at `~/.kube/config`
- `kubectl get pods -A` is short for `kubectl get pods --all-namespaces`
- If `kubectl` cannot connect, check if your cluster is running: `kind get clusters` or `minikube status`
- `-o wide` flag gives extra details: `kubectl get nodes -o wide`

---

## Documentation
Create `day-50-k8s-setup.md` with:
- Kubernetes history in your own words (3-4 sentences)
- Your architecture diagram (text-based or image)
- Which tool you chose (kind/minikube) and why
- Screenshot of `kubectl get nodes` and `kubectl get pods -n kube-system`
- What each kube-system pod does

---

