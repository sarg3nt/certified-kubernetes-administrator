# IP Address Management (IPAM)

How the IPs are assigned to the pods in the nodes.

The CNI config file `/etc/cni/net.d/net-script.conf` has a section called `ipam` that lets us specify which type of plugin should be used, the subnet and route.