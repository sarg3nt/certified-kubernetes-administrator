# Prerequisite - Container Networking Interface (CNI)

CNI standardizes what features container runtimes should have for networking and how networking plugins built to provide the tooling needing for setting up Container Networks should work.

rkt, Mesos and Kubernetes all use CNI, Docker does not, it has its own thing called CNM (container network model)

When Kubernetes creates a container using Docker it invokes them on the `none` network and then invokes the CNI plugin manually.  