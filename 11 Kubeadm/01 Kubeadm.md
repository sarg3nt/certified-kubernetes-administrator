# Kubeadm

## Overview 

1. Must have multiple systems or VMs for provisioning the cluster
2. Install a container runtime (Docker)
3. Install kubeadm on all the nodes
4. Initialize the Master server
5. Create the special POD network between the nodes
6. Join the worker nodes to the master nodes

## Resources

He used Vagrant to create the 3 nodes.  Vagrant code is below with docs.  
You need Vagrant and virtual box installed 

The vagrant file used in the next video is available here:  
https://github.com/kodekloudhub/certified-kubernetes-administrator-course

Kubeadm docs:  
https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/