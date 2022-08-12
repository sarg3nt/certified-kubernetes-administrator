# Kubernetes Interfaces

## Container Runtime Interface (CRI)

Kubernetes originally worked with just Docker and all of the code to talk to Docker was hard coded in.  When other container runtimes became available (rkt, cri-o) the Container Runtime Interface was created to abstract the communication to these runtimes.

## Container Networking Interface (CNI)

An interface many different networking layers such as weaveworks, flannel, cilium, etc.

## Container Storage Interface (CSI)

An interface to work with many different storage solutions.

Note that CSI is not a k8s specific standard but a universal interface to storage. Currently Kubernetes, Cloud Foundry and Mesos are using CSI.

CSI defines a set of Remote Procedure Calls (RPCs) that a storage driver must implement to be compliant.