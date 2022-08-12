# Cluster Maintenance

## Pod Eviction on Down Node

When a node goes down, the Kube Controller Manager waits 5 minutes for it to come back up, if it does not, those pods part of a replica set will be started on other nodes, this time is known as the "pod eviction timeout" and is configurable by setting:  
`kube-controller-manager --pod-eviction-timeout=5m0s`

## Draining a Node

`kubectl drain node-1`  
may need to force with  
`kubectl drain node-1 --ignore-daemonsets=true`   
There are many other flags that may need to be used like `--force`

Do maintenance then  
`kubectl uncordon node-1`  
