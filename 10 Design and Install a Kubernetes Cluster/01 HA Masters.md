# HA Masters

Master nodes consist of:

- ETCD
- API Server
- Controller Manager
- Scheduler

## API Server

The API Server runs in Active / Active mode, meaning all of the API Servers are accepting requests and doing work.  
Since we point `kubectl` at the kube-api it should be behind a load balancer.

## Controller Manager and Scheduler

These work in Active / Standby via a leader election process.  

kube-controller has the following settings:  
```bash
--leader-elect true
--leader-elect-lease-duration 15s
--leader-elect-renew-deadline 10s
--leader-elect-retry-period 2s
```

The first kubec-controller that comes up takes a lock `kube-controller-manager` endpoint object in the cluster and sets its config data there.  Every 2 seconds by default other kube-controllers try so if one crashes another can take over.

## etcd

The "Stacked Control Plane Nodes Topology" model has etcd on the same nodes as the rest of the control plane, this uses the least amount fo resources and nodes but can have scaling issues.  

The "External ETCD Topology" model has etcd running on it's own servers.  This is less risky as a failed control plane node does not harm the etcd cluster but is harder to set up and takes more resources.

We just need to make sure that the kube-apiserver has all of the etcd node addresses specified in its `--etcd-servers` attribute.  i.e. `--etcd-server=https://10.240.0.10:2379,https://10.240.0.11:2379,https://10.240.0.12:2379.`  
The api server can read and write to any of the etcd instances, thus the list.

### etcdctl

```bash
export ETCDCTL_API=3

etcdctl put name john
etcdctl get name
# name
# john

etcdctl get / --prefix --keys-only
# name
```

## etcd FAQs

From: https://etcd.io/docs/v3.3/faq/  

### What is maximum cluster size?

Theoretically, there is no hard limit. However, an etcd cluster probably should have no more than seven nodes. Google Chubby lock service, similar to etcd and widely deployed within Google for many years, suggests running five nodes. A 5-member etcd cluster can tolerate two member failures, which is enough in most cases. Although larger clusters provide better fault tolerance, the write performance suffers because data must be replicated across more machines

### Does etcd work in cross-region or cross data center deployments?

Deploying etcd across regions improves etcd’s fault tolerance since members are in separate failure domains. The cost is higher consensus request latency from crossing data center boundaries. Since etcd relies on a member quorum for consensus, the latency from crossing data centers will be somewhat pronounced because at least a majority of cluster members must respond to consensus requests. Additionally, cluster data must be replicated across all peers, so there will be bandwidth cost as well.

With longer latencies, the default etcd configuration may cause frequent elections or heartbeat timeouts. See tuning for adjusting timeouts for high latency deployments.
