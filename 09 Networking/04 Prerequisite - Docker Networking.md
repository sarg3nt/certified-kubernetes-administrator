# Prerequisite - Docker Networking

```bash
# Run an nginx pod with no network at all
docker run --network none nginx

# On the host network, no additional port mapping required but only one process can listen on a port at once
docker run --network host nginx

# A bridge network using the same technology we looked at in the previous Networking module.
docker run -P --network bridge nginx
# This sets up the network namespace, a bridge, the connections and NAT for the port mapping
```