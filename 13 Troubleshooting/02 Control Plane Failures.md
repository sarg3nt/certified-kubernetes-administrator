# Control Plane Failures

- Check node status: `k get nodes`
- Check status of pods running on the cluster: `k get pods -A`
- Check control plane services:
    ```bash 
    # If running as pods then get and describe the pods
    k get po -n kube-system kube-apiserver-master

    # If running as services:
    # Masters
    service kube-apiserver status
    service kube-controller-manager status
    service kube-scheduler status

    # Workers
    service kubelet status
    service kube-proxy status
    ```
- Check Service Logs:  
    ```bash
    # If running as containers then use 
    k logs -n kube-system kube-apiserver-master

    # If running as services then use the logging solutions, for ubuntu:
    sudo journalctl -u kube-apiserver
    ```
See docs for more on debugging clusters:  
https://kubernetes.io/docs/tasks/debug/debug-cluster/