# Backing up Etcd

## Take a Snapshot

```bash
# The below certs come from here for a typical kubeadm install.
# You can find them by doing `kubectl describe pod -n kube-system etcd-controlplane` or the equivalent and looking at the command args.
#  PARAM TO ETCD,  PARAM FROM RUNNING COMMAND ARGS
# --endpoints   --> --listen-client-urls
# --cacert      --> --trusted-ca-file
# --cert        --> --cert-file
# --key         --> --key-file=/etc/kubernetes/pki/etcd/server.key

export ETCDCTL_API=3 # this is not necessary in newer versions of etcdctl as it becomes the default.
etcdctl snapshot save \
  --endpoints=127.0.0.1:2379 \
  --cacert=/etc/kubernetes/pki/etcd/ca.crt \
  --cert=/etc/kubernetes/pki/etcd/server.crt \
  --key=/etc/kubernetes/pki/etcd/server.key \
  /opt/snapshot-pre-boot.db
```

## Snapshot Status 

```bash
ETCDCTL_API=3 etcdctl snapshot status /opt/snapshot-pre-boot.db
# Output: 64215292, 2212, 964, 2.1 MB
```

## Restore a Snapshot

To restore the etcd snapshot    
```bash
export ETCDCTL_API=3 # this is not necessary in newer versions of etcdctl as 

service kube-apiserver stop

# No server certs or endpoints are required here because we are not talking 
# to the etcd server, we are simply extracting the backup to the target folder.

etcdctl snapshot restore \
  --data-dir /var/lib/etcd-from-backup \
  /opt/snapshot-pre-boot.db

# Output:
# 2022-07-29 16:40:00.404296 I | mvcc: restore compact to 1658
# 2022-07-29 16:40:00.411177 I | etcdserver/membership: added member 8e9e05c52164694d [http://localhost:2380] to cluster cdf818194e3a8c32

# etcd is deployed as a static pod in this config so we need to edit the manifest file directly to change the data directory for etcd
nano /etc/kubernetes/manifests/etcd.yaml

# Edit volumes: hostpath: etcd-data ---> path: to the new path we restored to above, which was /var/lib/etcd-from-backup

# Wait for the pods to come up.  
# May need to delete the etcd pod as it does not always come back up correctly the first time.
  
```

# Certification Exam Tip!

Here's a quick tip. In the exam, you won't know if what you did is correct or not as in the practice tests in this course. You must verify your work yourself. For example, if the question is to create a pod with a specific image, you must run the the kubectl describe pod command to verify the pod is created with the correct name and correct image.

# References

https://kubernetes.io/docs/tasks/administer-cluster/configure-upgrade-etcd/#backing-up-an-etcd-cluster

https://github.com/etcd-io/website/blob/main/content/en/docs/v3.5/op-guide/recovery.md

https://www.youtube.com/watch?v=qRPNuT080Hk