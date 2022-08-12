# Cluster DNS

## Basics 

For a service called `web-service` created in the `apps` namespace the following DNS record would be created.  Also showing the DNS record for the pod if that is enabled.

| Hostname | Namespace | Type | Root | IP Address
| -------- | --------- | ---- |  --- | ------- |
| web-service | apps | svc | cluster.local | 10.107.37.188 |
| 10-244-2-5 | apps | pod | cluster.local | 10.244.2.5 |

Communication inside the same namespace can use the "Hostname" which is the name of the service.  Communication from namespace to namespace must use some or all of the FQDN which would be `web-service.apps.svc.cluster.local` but could be just `web-service.apps`  

Records for pods are not enabled by default but can be enabled.  If enabled, the Hostname for the pod is the ip with the dots replaced with dashes.

## Implementation

Kubernetes now uses CoreDNS which is deployed as a replicaset in the `kube-system` namespace.  It is configured via the `/etc/coredns/Corfile` file which is passed in as a `configmap` called `coredns`

CoreDNS works with plugins.  The plugin that makes CoreDNS work with Kubernetes is the `kubernetes` plugin.  Others include `errors`, `logging`, etc.  
The CoreDNS `kubernetes` plugin config is where the clusters `cluster.local` top level domain is set and options such as creating DNS records for pods are set.

When the CoreDNS container is deployed, it creates a service called `kube-dns` by default, and this services IP is used by the kubelet to assign the nameserver when each pod is created, it does this by adding an entry to the `/etc/resolv.conf` file like this `nameserver 10.43.0.10`  
A `search` entry is also added with DNS search information like this: `default.svc.cluster.local svc.cluster.local cluster.local` where the first entry contains the name of the namespace, `default` in this case.  

The kubelet config file at `/var/lib/kubelet/config.yaml` will contain the `clusterDNS` and `clusterDomain` as configured by CoreDNS.




