# Persistent Volume Claims

PVs and PVCs are two different things.  PVs are created by an administrator or CSI Storage Class Driver while PVCs are created by an end user to claim the PV.

When a user creates a PVC, Kubernetes tries to find a PV that matches the requirements.  K8s checks for sufficient capacity, Access Modes, Volume Modes, Storage Class, and Selectors.  

A smaller claim can be bound to a larger volume if there are no better options.  There is a 1 to 1 relationship between claims and volumes, so no other claim can attach to that volume.

If there are no other volumes available and PVC will remain in a pending state until newer volumes are made available to the cluster.  Once new claims are available, the PVC will bind to it.

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: my-claim
spec:
  accessModes: 
    - ReadWriteOnce
  resources: 
    requests: 
      storage: 500Mi
```

```bash
kubectl get persistentvolumeclaim
kubectl delete persistentvolumeclaim <name>
```

A PVC with a storageClassName assigned.  Note, the storage class doesn't actually have to exist if there is a PV with that storage class name it will bind.
```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mysql-alpha-pvc
  namespace: alpha
spec:
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
  storageClassName: slow
```

What happens when a PVC is deleted depends on the `persistentVolumeReclaimPolicy` applied to the volume.  The default `persistentVolumeReclaimPolicy` is `Retain` but it can be set to `Delete`.  There is a `Recycle` option that is deprecated.

## Using PVCs in PODs

Notes from the lecture.

Once you create a PVC use it in a POD definition file by specifying the PVC Claim name under `persistentVolumeClaim` section in the volumes section like this:


```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
    - name: myfrontend
      image: nginx
      volumeMounts:
      - mountPath: "/var/www/html"
        name: mypd
  volumes:
    - name: mypd
      persistentVolumeClaim:
        claimName: myclaim
```

The same is true for ReplicaSets or Deployments. Add this to the pod template section of a Deployment on ReplicaSet.

Reference URL: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#claims-as-volumes