# Worker Node Failures

- Examine nodes with `k get nodes` and `k describe nodes`
- If node statuses are set to `unknown` this can indicate the possible loss of the node.  Check the heartbeat field for last time it reported
- Check node itself from ssh
- Check for CPU, Memory and Disk Space issues on the nodes using `top`, `df -h`, `free -h`
- Check the status of the kubelet:
`service kubelet status`  
`sudo journalctl -u kubelet`
- Check kubelet certificates
`openssl x509 -in /var/lib/kubelet/pki/kubelet.crt -text`  
  - Check their expire data
  - Check the Issuer, is it the right CN
  - Check their security group, should be `Subject CN = system:node:worker-1, O = system.nodes`  where `O` should be `system.nodes`
- Check kubelet config in:  
`/etc/kubernetes/kubelet.conf` this file holds how the kubelet will talk to the k8s api
`/var/lib/kubelet/config.yaml`