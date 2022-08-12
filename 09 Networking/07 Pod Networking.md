# Pod Networking

This is the internal network layer that allows pods to talk to each other.  
By default Kubernetes does not ship with an internal network stack but it does have a well defined set of requirements.

Most of the networking is what we've already seen in network namespaces but we have to add node to node routing.
`ip route add <pod ip> via <remote-node-ip>`  
This will get very complicated very quickly, the better way is to use a router on your network and add the routes there.  

CNI (container networking interface) defines a standard way of ADDing and DELeting pod namespaces, ips, routes, etc. so as a "network script" developer I can write that script knowing what I need to implement.  
The kubelet starts new pds and via configuration runs the network script.  
These values are part of the kubelet configuration:  
`--cni-conf-dir=/etc/cni/net.d` holds the configuration for my script and  
`--cni-bin-dir=/etc/cni/bin` the binary of the script --> `./net-script.sh add <container> <namespace>`