# Day 51 – Kubernetes Manifests and Your First Pods

---

## The Anatomy of a Kubernetes Manifest

Every Kubernetes resource is defined using a YAML manifest with four required top-level fields:

```yaml
apiVersion: v1          # Which API version to use
kind: Pod               # What type of resource
metadata:               # Name, labels, namespace
  name: my-pod
  labels:
    app: my-app
spec:                   # The actual specification (what you want)
  containers:
  - name: my-container
    image: nginx:latest
    ports:
    - containerPort: 80
```

- `apiVersion` — tells Kubernetes which API group to use. For Pods, it is `v1`.
- `kind` — the resource type. Today it is `Pod`. Later you will use `Deployment`, `Service`, etc.
- `metadata` — the identity of your resource. `name` is required. `labels` are key-value pairs used for organization and selection.
- `spec` — the desired state. For a Pod, this means which containers to run, which images, which ports, etc.

---

## Challenge Tasks

### Task 1: Create Your First Pod (Nginx)
Create a file called `nginx-pod.yaml`:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
  labels:
    app: nginx
spec:
  containers:
  - name: nginx
    image: nginx:latest
    ports:
    - containerPort: 80
```

Apply it:
```bash
kubectl apply -f nginx-pod.yaml
```

Verify:
```bash
kubectl get pods
kubectl get pods -o wide
```

Wait until the STATUS shows `Running`. Then explore:
```bash
# Detailed info about the pod
kubectl describe pod nginx-pod

# Read the logs
kubectl logs nginx-pod

# Get a shell inside the container
kubectl exec -it nginx-pod -- /bin/bash

# Inside the container, run:
curl localhost:80
exit
```

```
jeenicj@DESKTOP-BG3MAVI:~/day51$ kubectl apply -f nginx-pod.yml
pod/nginx-pod created

jeenicj@DESKTOP-BG3MAVI:~/day51$ kubectl get pods -o wide
NAME        READY   STATUS    RESTARTS   AGE   IP           NODE                           NOMINATED NODE   READINESS GATES
nginx-pod   1/1     Running   0          29s   10.244.0.5   devops-cluster-control-plane   <none>           <none>

jeenicj@DESKTOP-BG3MAVI:~/day51$ kubectl get pods
NAME        READY   STATUS    RESTARTS   AGE
nginx-pod   1/1     Running   0          60s

jeenicj@DESKTOP-BG3MAVI:~/day51$ kubectl exec -it nginx-pod -- bash
root@nginx-pod:/# curl localhost:80
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>

```

---

### Task 2: Create a Custom Pod (BusyBox)
Write a new manifest `busybox-pod.yaml` from scratch (do not copy-paste the nginx one):

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: busybox-pod
  labels:
    app: busybox
    environment: dev
spec:
  containers:
  - name: busybox
    image: busybox:latest
    command: ["sh", "-c", "echo Hello from BusyBox && sleep 3600"]
```

Apply and verify:
```bash
kubectl apply -f busybox-pod.yaml
kubectl get pods
kubectl logs busybox-pod
```

Notice the `command` field — BusyBox does not run a long-lived server like Nginx. Without a command that keeps it running, the container would exit immediately and the pod would go into `CrashLoopBackOff`.


```
jeenicj@DESKTOP-BG3MAVI:~/day51$ kubectl apply -f busybox-pod.yaml
pod/busybox-pod created

jeenicj@DESKTOP-BG3MAVI:~/day51$ kubectl get pods
NAME          READY   STATUS    RESTARTS   AGE
busybox-pod   1/1     Running   0          11s
nginx-pod     1/1     Running   0          36m

jeenicj@DESKTOP-BG3MAVI:~/day51$ kubectl logs busybox-pod
Hello from BusyBox

```

---

### Task 3: Imperative vs Declarative
You have been using the declarative approach (writing YAML, then `kubectl apply`). Kubernetes also supports imperative commands:

```bash
# Create a pod without a YAML file
kubectl run redis-pod --image=redis:latest

# Check it
kubectl get pods
```

Now extract the YAML that Kubernetes generated:
```bash
kubectl get pod redis-pod -o yaml
```

Compare this output with your hand-written manifests. Notice how much extra metadata Kubernetes adds automatically (status, timestamps, uid, resource version).

You can also use dry-run to generate YAML without creating anything:
```bash
kubectl run test-pod --image=nginx --dry-run=client -o yaml
```

This is a powerful trick — use it to quickly scaffold a manifest, then customize it.

**Verify:** Save the dry-run output to a file and compare its structure with your nginx-pod.yaml. What fields are the same? What is different?


| **Field** | **Handwritten YAML** | **Dry-run YAML** |
| --- | --- | --- |
| **[apiVersion](ca://s?q=Explain_apiVersion_in_Kubernetes)** | v1 | v1 |
| **[kind](ca://s?q=Explain_kind_in_Kubernetes)** | Pod | Pod |
| **[metadata.name](ca://s?q=Explain_metadata_name_in_Kubernetes)** | nginx-pod | test-pod |
| **[metadata.labels](ca://s?q=Explain_metadata_labels_in_Kubernetes)** | Maybe absent | run: test-pod |
| **[spec.containers](ca://s?q=Explain_spec_containers_in_Kubernetes)** | Present | Present |
| **[Container name](ca://s?q=Explain_container_name_in_Kubernetes)** | nginx | test-pod |
| **[Container image](ca://s?q=Explain_container_image_in_Kubernetes)** | nginx:latest | nginx |
| **[restartPolicy](ca://s?q=Explain_restartPolicy_in_Kubernetes)** | Maybe absent | Always |
| **[dnsPolicy](ca://s?q=Explain_dnsPolicy_in_Kubernetes)** | Maybe absent | ClusterFirst |
| **[resources](ca://s?q=Explain_resources_in_Kubernetes)** | Maybe absent | {} |
| **[status](ca://s?q=Explain_status_in_Kubernetes)** | Usually absent | {} |


---

### Task 4: Validate Before Applying
Before applying a manifest, you can validate it:

```bash
# Check if the YAML is valid without actually creating the resource
kubectl apply -f nginx-pod.yaml --dry-run=client

# Validate against the cluster's API (server-side validation)
kubectl apply -f nginx-pod.yaml --dry-run=server
```

Now intentionally break your YAML (remove the `image` field or add an invalid field) and run dry-run again. See what error you get.

**Verify:** What error does Kubernetes give when the image field is missing?


```
jeenicj@DESKTOP-BG3MAVI:~/day51$ kubectl apply -f nginx-pod.yml --dry-
run=client
pod/nginx-pod configured (dry run)

jeenicj@DESKTOP-BG3MAVI:~/day51$ kubectl apply -f nginx-pod.yml --dry-run=server
The Pod "nginx-pod" is invalid: spec.containers[0].image: Required value

```

---

### Task 5: Pod Labels and Filtering
Labels are how Kubernetes organizes and selects resources. You added labels in your manifests — now use them:

```bash
# List all pods with their labels
kubectl get pods --show-labels

# Filter pods by label
kubectl get pods -l app=nginx
kubectl get pods -l environment=dev

# Add a label to an existing pod
kubectl label pod nginx-pod environment=production

# Verify
kubectl get pods --show-labels

# Remove a label
kubectl label pod nginx-pod environment-
```

```
jeenicj@DESKTOP-BG3MAVI:~/day51$ kubectl get pods --show-labels
NAME          READY   STATUS    RESTARTS   AGE   LABELS
busybox-pod   1/1     Running   0          33m   app=busybox,environment=dev
nginx-pod     1/1     Running   0          69m   app=nginx
redis-pod     1/1     Running   0          16m   run=redis-pod

jeenicj@DESKTOP-BG3MAVI:~/day51$ kubectl get pods -l app=nginx
NAME        READY   STATUS    RESTARTS   AGE
nginx-pod   1/1     Running   0          69m

jeenicj@DESKTOP-BG3MAVI:~/day51$ kubectl get pods -l environment=dev
NAME          READY   STATUS    RESTARTS   AGE
busybox-pod   1/1     Running   0          33m

jeenicj@DESKTOP-BG3MAVI:~/day51$ kubectl label pod nginx-pod environment=production
pod/nginx-pod labeled

jeenicj@DESKTOP-BG3MAVI:~/day51$ kubectl get pods --show-labels
NAME          READY   STATUS    RESTARTS   AGE   LABELS
busybox-pod   1/1     Running   0          34m   app=busybox,environment=dev
nginx-pod     1/1     Running   0          71m   app=nginx,environment=production
redis-pod     1/1     Running   0          18m   run=redis-pod

jeenicj@DESKTOP-BG3MAVI:~/day51$ kubectl label pod nginx-pod environment-
pod/nginx-pod unlabeled

```


Write a manifest for a third pod with at least 3 labels (app, environment, team). Apply it and practice filtering.

```
jeenicj@DESKTOP-BG3MAVI:~/day51$ kubectl label pod redis-pod app=redis
pod/redis-pod labeled
jeenicj@DESKTOP-BG3MAVI:~/day51$ kubectl label pod redis-pod environment=test
pod/redis-pod labeled
jeenicj@DESKTOP-BG3MAVI:~/day51$ kubectl label pod redis-pod team=testing
pod/redis-pod labeled
jeenicj@DESKTOP-BG3MAVI:~/day51$ kubectl get pods --show-labels
NAME          READY   STATUS    RESTARTS   AGE   LABELS
busybox-pod   1/1     Running   0          40m   app=busybox,environment=dev
nginx-pod     1/1     Running   0          77m   app=nginx
redis-pod     1/1     Running   0          24m   app=redis,environment=test,run=redis-pod,team=testing
jeenicj@DESKTOP-BG3MAVI:~/day51$
```

---

### Task 6: Clean Up
Delete all the pods you created:

```bash
# Delete by name
kubectl delete pod nginx-pod
kubectl delete pod busybox-pod
kubectl delete pod redis-pod

# Or delete using the manifest file
kubectl delete -f nginx-pod.yaml

# Verify everything is gone
kubectl get pods
```

Notice that when you delete a standalone Pod, it is gone forever. There is no controller to recreate it. This is why in production you use Deployments (coming on Day 52) instead of bare Pods.


```
jeenicj@DESKTOP-BG3MAVI:~/day51$ kubectl delete pod nginx-pod.yml
Error from server (NotFound): pods "nginx-pod.yml" not found
jeenicj@DESKTOP-BG3MAVI:~/day51$ kubectl delete pod nginx-pod.yaml
Error from server (NotFound): pods "nginx-pod.yaml" not found
jeenicj@DESKTOP-BG3MAVI:~/day51$ kubectl delete pod nginx-pod
pod "nginx-pod" deleted from default namespace
jeenicj@DESKTOP-BG3MAVI:~/day51$ kubectl delete pod redis-pod
pod "redis-pod" deleted from default namespace
jeenicj@DESKTOP-BG3MAVI:~/day51$ kubectl delete pod busybox-pod
pod "busybox-pod" deleted from default namespace
jeenicj@DESKTOP-BG3MAVI:~/day51$ kubectl get pods
No resources found in default namespace.
jeenicj@DESKTOP-BG3MAVI:~/day51$
```

---


---

