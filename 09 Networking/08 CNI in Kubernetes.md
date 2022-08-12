# CNI in Kubernetes

The kubelet service configuration has an option for which CNI network plugin to use.  
`--network-plugin=cni`  This tells the kubelet to use a CNI plugin.  The plugins are typically stored in `/opt/cni/bin` but this can be configured with the `--cni-bin-dir=/opt/cni/bin` option.  
This folder contains all of the currently installed networking plugin binaries.

The config file(s) for which CNI plugin to use are stored in `/etc/cni/net.d` but this can be configured with the kubelet option `--cni-conf-dir=/etc/cni/net.d`.  
If there is more than one config file then they are loaded in alphabetical order with the first being the default.