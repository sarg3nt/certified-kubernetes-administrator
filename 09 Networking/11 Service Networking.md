# Service Networking

This talk focussed on how services get their IPs

## Service Types

- ClusterIP: Gets a cluster wide IP that is available on all nodes but not available outside the cluster
- NodePort: Same as ClusterIP but also gets a port from the assigned port range that is available externally  

## Kube Proxy

A service is a virtual object, there is no executable or linux service that gets created.  When the service is created it gets assigned an IP address from a pre-defined range.
The kube-proxy on every node watches for the service to be created and gets the assigned IP address and creates forwarding rules on each node saying any traffic coming to the IP of the service should go to the IP fo the pod(s)

Kube-proxy can create these rules via `userspace`, `ipvs` or the default of `iptables`.  This can be set via the kube-proxy setting `--proxy-mode [userspace, iptables, ipvs]`.  If this is not set, it defaults to `iptables`

The service IP range is set via the kube-api-server setting `--service-cluster-ip-range` which is by default `10.0.0.0/24`

To examine what the kube-api-server service ip range is set to run `ps -aux | grep kube-api-server` or check the kub-api-server config which may be at `/etc/kubernetes/manifests/kube-api-server.yaml`  

**NOTE:** The pod network and service network range should not overlap.

Search for the name of the rule in ip tables with:  
`iptabls -L -t nat | grep <service-name>`  
This works because all rules created by kube-proxy will have a comment with the service name.

We can see kube-proxy creating these rules in the logs with:  
`cat /var/log/kube-proxy.log`  
Example Output:  
`Using iptables Proxier.`  
`Adding new service "default/db-service:3306" at 10.103.132.104:3306/TCP`

