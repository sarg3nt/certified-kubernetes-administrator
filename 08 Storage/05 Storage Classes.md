# Storage Classes

## Static Provisioning

The previous example3s of an administrator manually creating a volume for a user is called Static Provisioning

## Dynamic Provisioning

Storage Classes enable automatic Dynamic Provisions of a volume based on a PVC.

A Storage Class for Googles Persistent Storage
```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata: 
  name: google-storage
provisioner: kubernetes.io/gce-pd
```

Once a Storage Class is Defined, it can be references in a PVC by adding `storageClassName: <storage-class-name>` to the `spec`

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: my-claim
spec:
  accessModes: 
    - ReadWriteOnce
  storageClassName: google-storage
  resources: 
    requests: 
      storage: 500Mi
```

There are many different Storage Class providers and each provide different capabilities that will need to be specified such as `type`, `replication-type`, etc.

You can create several Storage Class definitions for the same provider that offer different capabilities.  A `silver` class might offer spinning disks while a `gold` class offers SSDs and a `platinum` class offers SSDs with replication.

## VolumeBindingMode

The volumeBindingMode field controls when volume binding and dynamic provisioning should occur. When unset, "Immediate" mode is used by default.

The Immediate mode indicates that volume binding and dynamic provisioning occurs once the PersistentVolumeClaim is created. For storage backends that are topology-constrained and not globally accessible from all Nodes in the cluster, PersistentVolumes will be bound or provisioned without knowledge of the Pod's scheduling requirements. This may result in unschedulable Pods.

A cluster administrator can address this issue by specifying the WaitForFirstConsumer mode which will delay the binding and provisioning of a PersistentVolume until a Pod using the PersistentVolumeClaim is created. PersistentVolumes will be selected or provisioned conforming to the topology that is specified by the Pod's scheduling constraints. These include, but are not limited to, resource requirements, node selectors, pod affinity and anti-affinity, and taints and tolerations.