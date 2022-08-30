# Component Versions

No other component should be at a higher version than the `kube-apiserver` except for kubectl which can be one higher.

Other components than the api server can be at a lower version if needed.  The following is the supported formula.

`kube-apiserver` X == v1.10 

`controller-manager`  X-1 == v1.9 or v1.10
`kube-scheduler`      X-1 == v1.9 or v1.10

`kubelet`      X-2 == v1.8, v1.9 or v1.10
`kube-proxy`   X-2 == v1.8, v1.9 or v1.10

`kubectl`   X+1, X or X-1 = v1.9, v1.10 or v1.11

This variance is what allows for live upgrades as components can be a lower version while higher up components are upgraded.

Kubernetes supports up to the latest 3 minor versions.  1.10, 1.11 and 1.12 for example.  

**The recommended approach is to upgrade one minor version at a time.**  So, if we were on 1.10 and 1.13 is out, we should upgrade to 1.11, then 1.12, then 1.13

# Upgrade Strategies

## Masters

Upgrade the master(s) first to the next minor version, i.e. 1.10 to 1.11

## Workers

There are several strategies for upgrading the workers

1.  All at once.  This causes all of your apps to go down and is not typical
2.  One node or  one set of nodes at a time, draining and uncordoning them as you go.
3.  Add new nodes to the cluster, drain the old nodes and delete them from the cluster.

# Upgrading with kubeadm

Instructions can be found on the kubernetes documentation site.  Search for `kubeadm upgrade`  
https://kubernetes.io/docs/tasks/administer-cluster/kubeadm/kubeadm-upgrade/

`kubeadm upgrade plan` will produce a lot of great information for an upgrade strategy.  

## Upgrading the Master(s)

The `kubeadm` tool uses the same versioning as kubernetes.  So if you want to upgrade from kubernetes 1.10 to 1.13 you must first upgrade to 1.12, therefor you must first upgrade kubeadm to 1.12.  

```bash

apt update

# Upgrade kubeadm
apt-get install -y --allow-change-held-packages kubeadm=1.24.0-00

# Plan
kubeadm upgrade plan

# Use kubeadm to upgrade the master
sudo kubeadm upgrade apply v1.24.x

# Drain the node
kubectl drain <node-to-drain> --ignore-daemonsets

# See note below, you may not have kubelet installed on master nodes.
# Upgrade kubelet and kubectl
apt-get install -y --allow-change-held-packages kubelet=1.24.x-00 kubectl=1.24.x-00
sudo systemctl daemon-reload
sudo systemctl restart kubelet
```
**Note:** Depending on how k8s was installed, you may not have kubelet running on the master node.  If kubeadm or an equivalent was used to install kubernetes then the master node components were installed as pods and thus a kubelet service was installed.  If installed by hand as Linux services then there would not be a kubelet.

## Worker(s)

```bash
apt update

apt-get install -y --allow-change-held-packages kubeadm=1.24.x-00

sudo kubeadm upgrade node

kubectl drain node01 --ignore-daemonsets=true

apt-get install -y --allow-change-held-packages kubelet=1.24.x-00 kubectl=1.24.x-00

systemctl daemon-reload
systemctl restart kubelet

kubectl uncordon node01
```

Repeat for all the nodes.