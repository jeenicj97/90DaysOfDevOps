# Day 52 – Kubernetes Namespaces and Deployments

---

## Challenge Tasks

### Task 1: Explore Default Namespaces
Kubernetes comes with built-in namespaces. List them:

```bash
kubectl get namespaces
```

You should see at least:
- `default` — where your resources go if you do not specify a namespace
- `kube-system` — Kubernetes internal components (API server, scheduler, etc.)
- `kube-public` — publicly readable resources
- `kube-node-lease` — node heartbeat tracking

Check what is running inside `kube-system`:
```bash
kubectl get pods -n kube-system
```

These are the control plane components keeping your cluster alive. Do not touch them.

**Verify:** How many pods are running in `kube-system`?

```
jeenicj@DESKTOP-BG3MAVI:~/day51$ kubectl get namespaces
NAME                 STATUS   AGE
default              Active   104m
kube-node-lease      Active   104m
kube-public          Active   104m
kube-system          Active   104m
local-path-storage   Active   104m

jeenicj@DESKTOP-BG3MAVI:~/day51$ kubectl get pods -n kube-system
NAME                                                   READY   STATUS    RESTARTS      AGE
coredns-589f44dc88-k9jdj                               1/1     Running   0             104m
coredns-589f44dc88-xcxrb                               1/1     Running   0             104m
etcd-devops-cluster-control-plane                      1/1     Running   0             104m
kindnet-bcqp9                                          1/1     Running   0             104m
kube-apiserver-devops-cluster-control-plane            1/1     Running   0             104m
kube-controller-manager-devops-cluster-control-plane   1/1     Running   2 (39m ago)   104m
kube-proxy-htsqp                                       1/1     Running   0             104m
kube-scheduler-devops-cluster-control-plane            1/1     Running   2 (39m ago)   104m
jeenicj@DESKTOP-BG3MAVI:~/day51$

```

---

### Task 2: Create and Use Custom Namespaces
Create two namespaces — one for a development environment and one for staging:

```bash
kubectl create namespace dev
kubectl create namespace staging
```

Verify they exist:
```bash
kubectl get namespaces
```

You can also create a namespace from a manifest:
```yaml
# namespace.yaml
apiVersion: v1
kind: Namespace
metadata:
  name: production
```

```bash
kubectl apply -f namespace.yaml
```

Now run a pod in a specific namespace:
```bash
kubectl run nginx-dev --image=nginx:latest -n dev
kubectl run nginx-staging --image=nginx:latest -n staging
```

List pods across all namespaces:
```bash
kubectl get pods -A
```

Notice that `kubectl get pods` without `-n` only shows the `default` namespace. You must specify `-n <namespace>` or use `-A` to see everything.

**Verify:** Does `kubectl get pods` show these pods? What about `kubectl get pods -A`?


```
jeenicj@DESKTOP-BG3MAVI:~/day51$ kubectl create namespace dev
namespace/dev created

jeenicj@DESKTOP-BG3MAVI:~/day51$ kubectl create namespace staging
namespace/staging created

jeenicj@DESKTOP-BG3MAVI:~/day51$ kubectl get namespaces
NAME                 STATUS   AGE
default              Active   108m
dev                  Active   73s
kube-node-lease      Active   108m
kube-public          Active   108m
kube-system          Active   108m
local-path-storage   Active   108m
staging              Active   8s

jeenicj@DESKTOP-BG3MAVI:~/day51$ vim namespace.yaml
jeenicj@DESKTOP-BG3MAVI:~/day51$ kubectl apply -f namespace.yaml
namespace/production created

jeenicj@DESKTOP-BG3MAVI:~/day51$ kubectl run nginx-dev --image=nginx:latest -n dev
pod/nginx-dev created

jeenicj@DESKTOP-BG3MAVI:~/day51$ kubectl run nginx-staging --image=nginx:latest -n staging
pod/nginx-staging created

jeenicj@DESKTOP-BG3MAVI:~/day51$ kubectl get pods -A
NAMESPACE            NAME                                                   READY   STATUS    RESTARTS      AGE
dev                  nginx-dev                                              1/1     Running   0             59s
kube-system          coredns-589f44dc88-k9jdj                               1/1     Running   0             111m
kube-system          coredns-589f44dc88-xcxrb                               1/1     Running   0             111m
kube-system          etcd-devops-cluster-control-plane                      1/1     Running   0             111m
kube-system          kindnet-bcqp9                                          1/1     Running   0             111m
kube-system          kube-apiserver-devops-cluster-control-plane            1/1     Running   0             111m
kube-system          kube-controller-manager-devops-cluster-control-plane   1/1     Running   2 (45m ago)   111m
kube-system          kube-proxy-htsqp                                       1/1     Running   0             111m
kube-system          kube-scheduler-devops-cluster-control-plane            1/1     Running   2 (45m ago)   111m
local-path-storage   local-path-provisioner-855c7b7774-nblh8                1/1     Running   0             111m
staging              nginx-staging                                          1/1     Running   0             8s

jeenicj@DESKTOP-BG3MAVI:~/day51$ kubectl get pods
No resources found in default namespace.

```


---

### Task 3: Create Your First Deployment
A Deployment tells Kubernetes: "I want X replicas of this Pod running at all times." If a Pod crashes, the Deployment controller recreates it automatically.

Create a file `nginx-deployment.yaml`:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  namespace: dev
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
      containers:
      - name: nginx
        image: nginx:1.24
        ports:
        - containerPort: 80
```

Key differences from a standalone Pod:
- `kind: Deployment` instead of `kind: Pod`
- `apiVersion: apps/v1` instead of `v1`
- `replicas: 3` tells Kubernetes to maintain 3 identical pods
- `selector.matchLabels` connects the Deployment to its Pods
- `template` is the Pod template — the Deployment creates Pods using this blueprint

Apply it:
```bash
kubectl apply -f nginx-deployment.yaml
```

Check the result:
```bash
kubectl get deployments -n dev
kubectl get pods -n dev
```

You should see 3 pods with names like `nginx-deployment-xxxxx-yyyyy`.

```
jeenicj@DESKTOP-BG3MAVI:~/day51$ vim nginx-deployment.yaml

jeenicj@DESKTOP-BG3MAVI:~/day51$ kubectl apply -f nginx-deployment.yaml
deployment.apps/nginx-deployment created

jeenicj@DESKTOP-BG3MAVI:~/day51$ kubectl get deployments -n dev
NAME               READY   UP-TO-DATE   AVAILABLE   AGE
nginx-deployment   0/3     3            0           14s

jeenicj@DESKTOP-BG3MAVI:~/day51$ kubectl get pods -n dev
NAME                                READY   STATUS    RESTARTS   AGE
nginx-deployment-68cd4c497b-d4lz8   1/1     Running   0          36s
nginx-deployment-68cd4c497b-t4z5g   1/1     Running   0          36s
nginx-deployment-68cd4c497b-zj8k5   1/1     Running   0          36s
nginx-dev                           1/1     Running   0          29m

```


**Verify:** What do the READY, UP-TO-DATE, and AVAILABLE columns mean in the deployment output?  
  * READY: The number of pods that are healthy and passing their readiness probes, shown as current / desired.
  * UP-TO-DATE: The number of pods that have been updated to the latest configuration (spec) defined in your YAML.
  * AVAILABLE: The number of pods currently running and ready to serve traffic.  

---

### Task 4: Self-Healing — Delete a Pod and Watch It Come Back
This is the key difference between a Deployment and a standalone Pod.

```bash
# List pods
kubectl get pods -n dev

# Delete one of the deployment's pods (use an actual pod name from your output)
kubectl delete pod <pod-name> -n dev

# Immediately check again
kubectl get pods -n dev
```

The Deployment controller detects that only 2 of 3 desired replicas exist and immediately creates a new one. The deleted pod is replaced within seconds.

**Verify:** Is the replacement pod's name the same as the one you deleted, or different?  
  * It is different. nginx-deployment-68cd4c497b-d4lz8 -> nginx-deployment-68cd4c497b-c28s8

```
jeenicj@DESKTOP-BG3MAVI:~/day51$ kubectl get pods -n dev
NAME                                READY   STATUS    RESTARTS   AGE
nginx-deployment-68cd4c497b-d4lz8   1/1     Running   0          16m
nginx-deployment-68cd4c497b-t4z5g   1/1     Running   0          16m
nginx-deployment-68cd4c497b-zj8k5   1/1     Running   0          16m
nginx-dev                           1/1     Running   0          45m

jeenicj@DESKTOP-BG3MAVI:~/day51$ kubectl delete pod nginx-deployment-68cd4c497b-d4lz8 -n dev
pod "nginx-deployment-68cd4c497b-d4lz8" deleted from dev namespace

jeenicj@DESKTOP-BG3MAVI:~/day51$ kubectl get pod -n dev
NAME                                READY   STATUS    RESTARTS   AGE
nginx-deployment-68cd4c497b-c28s8   1/1     Running   0          11s
nginx-deployment-68cd4c497b-t4z5g   1/1     Running   0          18m
nginx-deployment-68cd4c497b-zj8k5   1/1     Running   0          18m
nginx-dev                           1/1     Running   0          46m

```


---

### Task 5: Scale the Deployment
Change the number of replicas:

```bash
# Scale up to 5
kubectl scale deployment nginx-deployment --replicas=5 -n dev
kubectl get pods -n dev

# Scale down to 2
kubectl scale deployment nginx-deployment --replicas=2 -n dev
kubectl get pods -n dev
```

Watch how Kubernetes creates or terminates pods to match the desired count.

```
jeenicj@DESKTOP-BG3MAVI:~/day51$ kubectl scale deployment nginx-deployment --replicas=5 -n dev
deployment.apps/nginx-deployment scaled
jeenicj@DESKTOP-BG3MAVI:~/day51$ kubectl get pods -n dev
NAME                                READY   STATUS    RESTARTS   AGE
nginx-deployment-68cd4c497b-c28s8   1/1     Running   0          3m27s
nginx-deployment-68cd4c497b-d4r5q   1/1     Running   0          9s
nginx-deployment-68cd4c497b-fqmt7   1/1     Running   0          9s
nginx-deployment-68cd4c497b-t4z5g   1/1     Running   0          21m
nginx-deployment-68cd4c497b-zj8k5   1/1     Running   0          21m
nginx-dev                           1/1     Running   0          49m
jeenicj@DESKTOP-BG3MAVI:~/day51$ kubectl scale deployment nginx-deployment --replicas=2 -n dev
deployment.apps/nginx-deployment scaled
jeenicj@DESKTOP-BG3MAVI:~/day51$ kubectl get pods -n dev
NAME                                READY   STATUS    RESTARTS   AGE
nginx-deployment-68cd4c497b-t4z5g   1/1     Running   0          21m
nginx-deployment-68cd4c497b-zj8k5   1/1     Running   0          21m
nginx-dev                           1/1     Running   0          50m
jeenicj@DESKTOP-BG3MAVI:~/day51$

```

You can also scale by editing the manifest — change `replicas: 4` in your YAML file and run `kubectl apply -f nginx-deployment.yaml` again.

**Verify:** When you scaled down from 5 to 2, what happened to the extra pods?
  * Kubernetes deletes the extra pods.

---

### Task 6: Rolling Update
Update the Nginx image version to trigger a rolling update:

```bash
kubectl set image deployment/nginx-deployment nginx=nginx:1.25 -n dev
```

Watch the rollout in real time:
```bash
kubectl rollout status deployment/nginx-deployment -n dev
```

Kubernetes replaces pods one by one — old pods are terminated only after new ones are healthy. This means zero downtime.

Check the rollout history:
```bash
kubectl rollout history deployment/nginx-deployment -n dev
```

Now roll back to the previous version:
```bash
kubectl rollout undo deployment/nginx-deployment -n dev
kubectl rollout status deployment/nginx-deployment -n dev
```

Verify the image is back to the previous version:
```bash
kubectl describe deployment nginx-deployment -n dev | grep Image
```

**Verify:** What image version is running after the rollback?


```
jeenicj@DESKTOP-BG3MAVI:~/day51$ kubectl set image deployment/nginx-deployment nginx=nginx:1.25 -n dev
deployment.apps/nginx-deployment image updated

jeenicj@DESKTOP-BG3MAVI:~/day51$ kubectl rollout history deployment/nginx-deployment -n dev
deployment.apps/nginx-deployment
REVISION  CHANGE-CAUSE
1         <none>
2         <none>

jeenicj@DESKTOP-BG3MAVI:~/day51$ kubectl rollout undo deployment/nginx-deployment -n dev
Warning: resource deployments/nginx-deployment was previously managed with 'kubectl apply'. Rolling back will not update the kubectl.kubernetes.io/last-applied-configuration annotation, which may cause unexpected behavior on future 'kubectl apply' operations. Consider using 'kubectl apply' with your previous configuration file instead.
deployment.apps/nginx-deployment rolled back

jeenicj@DESKTOP-BG3MAVI:~/day51$ kubectl rollout status deployment/nginx-deployment -n dev
deployment "nginx-deployment" successfully rolled out

jeenicj@DESKTOP-BG3MAVI:~/day51$ kubectl describe deployment nginx-deployment -n dev | grep Image
    Image:         nginx:1.24

```

---

### Task 7: Clean Up
```bash
kubectl delete deployment nginx-deployment -n dev
kubectl delete pod nginx-dev -n dev
kubectl delete pod nginx-staging -n staging
kubectl delete namespace dev staging production
```

Deleting a namespace removes everything inside it. Be very careful with this in production.

```bash
kubectl get namespaces
kubectl get pods -A
```

**Verify:** Are all your resources gone?
   * Yes

```
jeenicj@DESKTOP-BG3MAVI:~/day51$ kubectl get namespace
NAME                 STATUS   AGE
default              Active   3h3m
kube-node-lease      Active   3h3m
kube-public          Active   3h3m
kube-system          Active   3h3m
local-path-storage   Active   3h3m
jeenicj@DESKTOP-BG3MAVI:~/day51$ kubectl get pods -A
NAMESPACE            NAME                                                   READY   STATUS    RESTARTS      AGE
kube-system          coredns-589f44dc88-k9jdj                               1/1     Running   0             3h3m
kube-system          coredns-589f44dc88-xcxrb                               1/1     Running   0             3h3m
kube-system          etcd-devops-cluster-control-plane                      1/1     Running   0             3h3m
kube-system          kindnet-bcqp9                                          1/1     Running   0             3h3m
kube-system          kube-apiserver-devops-cluster-control-plane            1/1     Running   0             3h3m
kube-system          kube-controller-manager-devops-cluster-control-plane   1/1     Running   3 (67m ago)   3h3m
kube-system          kube-proxy-htsqp                                       1/1     Running   0             3h3m
kube-system          kube-scheduler-devops-cluster-control-plane            1/1     Running   3 (67m ago)   3h3m
local-path-storage   local-path-provisioner-855c7b7774-nblh8                1/1     Running   0             3h3m
```

---
