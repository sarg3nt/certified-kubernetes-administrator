# Weaveworks CNI Plugin

Weave creates an agent on each node that discovers the IPs of all the other nodes and their pods.  When a request is sent to another node/pod, weave gets the data, packages it and delivers it to the correct node where the destination agent unpackages it and delivers it to the destination pod.

## Deploy Weave

Weave can be installed on the nodes pre cluster build or deployed on a cluster that exists as a set of daemon pods.

`kubectl apply -f "https://cloud.weave.works/k8us/net?k8s-version=$(kubectl version | base64 | tr -d '\n')"`