# Day 55 – Persistent Volumes (PV) and Persistent Volume Claims (PVC)

---

## Challenge Tasks

### Task 1: See the Problem — Data Lost on Pod Deletion
1. Write a Pod manifest that uses an `emptyDir` volume and writes a timestamped message to `/data/message.txt`
2. Apply it, verify the data exists with `kubectl exec`
3. Delete the Pod, recreate it, check the file again — the old message is gone

**Verify:** Is the timestamp the same or different after recreation?  
 > Different. The emptyDir volume exists only for the lifetime of the Pod. When the Pod is deleted, all data is lost.

![Image Alt](https://github.com/jeenicj97/90DaysOfDevOps/blob/master/2026/day-55/day55-task1.jpg)

---

### Task 2: Create a PersistentVolume (Static Provisioning)
1. Write a PV manifest with `capacity: 1Gi`, `accessModes: ReadWriteOnce`, `persistentVolumeReclaimPolicy: Retain`, and `hostPath` pointing to `/tmp/k8s-pv-data`
2. Apply it and check `kubectl get pv` — status should be `Available`

Access modes to know:
- `ReadWriteOnce (RWO)` — read-write by a single node
- `ReadOnlyMany (ROX)` — read-only by many nodes
- `ReadWriteMany (RWX)` — read-write by many nodes

`hostPath` is fine for learning, not for production.

**Verify:** What is the STATUS of the PV?
 > Available

![Image Alt](https://github.com/jeenicj97/90DaysOfDevOps/blob/master/2026/day-55/day55-task2.jpg)

---

### Task 3: Create a PersistentVolumeClaim
1. Write a PVC manifest requesting `500Mi` of storage with `ReadWriteOnce` access
2. Apply it and check both `kubectl get pvc` and `kubectl get pv`
3. Both should show `Bound` — Kubernetes matched them by capacity and access mode

**Verify:** What does the VOLUME column in `kubectl get pvc` show?  
 > It shows manual-pv, indicating that the PVC has successfully bound to the PersistentVolume.

![Image Alt](https://github.com/jeenicj97/90DaysOfDevOps/blob/master/2026/day-55/day55-task3.1.jpg)

![Image Alt](https://github.com/jeenicj97/90DaysOfDevOps/blob/master/2026/day-55/day55-task3.2.jpg)

---

### Task 4: Use the PVC in a Pod — Data That Survives
1. Write a Pod manifest that mounts the PVC at `/data` using `persistentVolumeClaim.claimName`
2. Write data to `/data/message.txt`, then delete and recreate the Pod
3. Check the file — it should contain data from both Pods

**Verify:** Does the file contain data from both the first and second Pod?  
 > Yes. The data persists because it is stored on the PersistentVolume instead of inside the Pod.

![Image Alt](https://github.com/jeenicj97/90DaysOfDevOps/blob/master/2026/day-55/day55-task4.jpg)


---

### Task 5: StorageClasses and Dynamic Provisioning
1. Run `kubectl get storageclass` and `kubectl describe storageclass`
2. Note the provisioner, reclaim policy, and volume binding mode
3. With dynamic provisioning, developers only create PVCs — the StorageClass handles PV creation automatically

**Verify:** What is the default StorageClass in your cluster?  
 > Standard

![Image Alt](https://github.com/jeenicj97/90DaysOfDevOps/blob/master/2026/day-55/day55-task6.jpg)

---

### Task 6: Dynamic Provisioning
1. Write a PVC manifest that includes `storageClassName: standard` (or your cluster's default)
2. Apply it — a PV should appear automatically in `kubectl get pv`
3. Use this PVC in a Pod, write data, verify it works

**Verify:** How many PVs exist now? Which was manual, which was dynamic?  
 > pv-manual is the manual one & pvc-47c2... is the dynamic one

![Image Alt](https://github.com/jeenicj97/90DaysOfDevOps/blob/master/2026/day-55/day55-task6.jpg)

---

### Task 7: Clean Up
1. Delete all pods first
2. Delete PVCs — check `kubectl get pv` to see what happened
3. The dynamic PV is gone (Delete reclaim policy). The manual PV shows `Released` (Retain policy).
4. Delete the remaining PV manually

**Verify:** Which PV was auto-deleted and which was retained? Why?  
 > Dynamic PV was automatically deleted because its StorageClass uses the Delete reclaim policy.  
 > Manual PV remained in the Released state because it uses the Retain reclaim policy, preserving the stored data until it is manually deleted.

![Image Alt](https://github.com/jeenicj97/90DaysOfDevOps/blob/master/2026/day-55/day55-task7.jpg)

---
