# Static Pods

## How to tell if a Pod is a Static Pod

1. Static pods will have the the name of the node as the last bit.  Example: mypod-node01
2. Look at the YAML for the pod, the `ownerReference` --> `kind` will be `node`

## Static Pod Manifest Location

The default is `/etc/kubernetes/manifests` but you can find the configured value by looking at the kubelet config here: `/var/lib/kubelet/config.yaml`  
The `staticPodPath` value will contain the file path.