# Persistent Volumes

## Volumes

One of the simplest volume types is a directory on the host.  
This works fine on a simple node but is not recommended on a multi-node cluster.
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: random-number-generator
spec:
  containers:
  - image: alpine
    name: alpine
    command: ["/bin/sh","-c"]
    args: ["shuf -i 0-100 -n 1 >> /opt/number.out;"]
    volumeMounts:
    - mountPath: /opt
      name: data-volume
  volumes:
  - name: data-volume
    hostPath:
      path: /data
      type: Directory
```

This show a persistent volume that would be claimable by a persistent volume claim.  
Note: This shows a hostPath being used, this should be used in a production cluster.

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-vol1
spec:
  accessModes:
    - ReadWriteOnce
  capacity:
    storage: 1Gi
  persistentVolumeReclaimPolicy: Retain
  hostPath:
    path: /tmp/data/
  # In prod we'd use a provider like the one below and not the hostPath
  awsElasticBlockStore:
    volumeID: <volume-id>
    fsType: ext4
```

View persistent volumes with `k get persistentvolume`