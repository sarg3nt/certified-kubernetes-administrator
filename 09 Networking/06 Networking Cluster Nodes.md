# Networking Cluster Nodes

See the Ports and Protocols document in the Kubernetes documentation for the list of ports that need to be accessible on the nodes.  
https://kubernetes.io/docs/reference/ports-and-protocols/

# Important Note about CNI and CKA Exam

**An important tip about deploying Network Addons in a Kubernetes cluster.**

**NOTE:** These docs might not be accurate any longer, it appears that later the instructor states this is no longer a question on the exam.  

In the upcoming labs, we will work with Network Addons. This includes installing a network plugin in the cluster. While we have used weave-net as an example, please bear in mind that you can use any of the plugins which are described here:

https://kubernetes.io/docs/concepts/cluster-administration/addons/

https://kubernetes.io/docs/concepts/cluster-administration/networking/#how-to-implement-the-kubernetes-networking-model

In the CKA exam, for a question that requires you to deploy a network addon, unless specifically directed, you may use any of the solutions described in the link above.

However, the documentation currently does not contain a direct reference to the exact command to be used to deploy a third party network addon.

The links above redirect to third party/ vendor sites or GitHub repositories which cannot be used in the exam. This has been intentionally done to keep the content in the Kubernetes documentation vendor-neutral.

At this moment in time, there is still one place within the documentation where you can find the exact command to deploy weave network addon:

https://v1-22.docs.kubernetes.io/docs/setup/production-environment/tools/kubeadm/high-availability/#steps-for-the-first-control-plane-node (step 2)