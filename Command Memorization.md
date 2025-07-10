# Command Memorization

List of commands to memorize.

## Main Memorization List

```bash
# Add the following to the .bashrc
export do="--dry-run=client -o yaml" # k create deploy nginx --image=nginx $do
export now="--force --grace-period 0" # k delete pod nginx $now
alias kn="kubectl config set-context --current --namespace" # kn default
alias knc="kubectl config view --minify | grep namespace" # Shows the current namespace
## nano coonfig
export KUBE_EDITOR=nano
# in the file (incorrect commands will be red, correct will turn green)
echo -e 'set tabsize 2\nset tabstospaces' > ~/.nanorc
# END OF .bashrc additions

# Show paths in jq
k get nodes -o json | jq -c 'paths'

# Kubelet Troubleshooting
ps -aux | grep kubelet

# To see what ports are open and how many connections they have open
netstat -plnt

# crt / pem checking
openssl x509  -noout -text -in /var/lib/kubelet/pki/kubelet-client-current.pem | grep "Extended Key Usage" 

ip link # interfaces
ip a # IP
ip route # # find default gateway
ps -aux | grep kubelet | grep network # Find what options a service is running with

## yq
yq eval ".user.addresses" user.yaml # Shows the array in array form
yq eval ".user.addresses[]" user.yaml # Flattens the array to KVPs (splat)
yq eval ".user.addresses[1]" user.yaml # Gets the second item in the array
yq eval '.user.orders[] | select(. == "43*")' user.yaml # gets order starting with 43

## etcd restore, stuff in docs is a bit misleading
# Step 1, move all the static pod yaml out of the manifest folder and wait for the containers to stop via crictl
# Step 2, use etcdctl to restore the snapshot, almost same as saving a snapshot, but with the 
# location to restore the --data-dir /var/lib/etcd-backup
ETCDCTL_API=3 etcdctl snapshot restore /tmp/etcd-backup.db \
--data-dir /var/lib/etcd-backup \
--cacert /etc/kubernetes/pki/etcd/ca.crt \
--cert /etc/kubernetes/pki/etcd/server.crt \
--key /etc/kubernetes/pki/etcd/server.key
# Step 3, change the etcd.yaml config to point to the new data-dir
# Step 4, move static pod yaml files back.

## Search Terms in Kube docs
# Expose pod information to containers with fieldRef
# Volume types are in the "Volumes" docs
# security context

k config set-context --current --namespace=<namespace-name>
k config view | grep namespace
k set image deployment/nginx nginx=nginx:1.9.1

systemctl status kubelet
service kubelet status

# crictl
# Search for "Mapping from dockercli to crictl"
ssh <target> 'crictl ps'
ssh <target> 'crictl logs <id>' &> /opt/file.txt

service kubelet status
# Look at Drop-In: line for where startup file is.
service kubelet start
which kubelet
# Explore config files at
# Kubelet config for talking to kube-api `/etc/kubernetes/kubelet.conf`
# Kubelet config: `/var/lib/kubelet/config.yaml`

# Logs for a service
journalctl -u <service-name>

k run busybox --image=busybox --rm -it -- nslookup <endpoint>
k run curl --image=alpine/curl --rm -it -- curl <endpoint>

## DNS
# Service FQDN is typically
<svc>.<namespace>.svc.cluster.local
my-service.default.svc.cluster.local

# Pod FQDN is typically
<pod-ip-dashed>.<namespace>.pod.cluster.local
10-5-2-7.default.pod.cluster.local

```

## Paths

| Item | Path |
| ----------- | ----------- |
| PKI Certs | `/etc/kubernetes/pki` |
| Static Pods | `/etc/kubernetes/manifests` |
| CNI Bin | `/opt/cni/bin/` |
| CNI config | `/etc/cni/net.d/` |
| Kubelet to kube-api | `/etc/kubernetes/kubelet.conf`|
| Kubelet config | `/var/lib/kubelet/config.yaml` |
| Kubelet Certs | `/var/lib/kubelet/pki/` |
| kubelet service start | `/etc/systemd/system/kubelet.service.d/10-kubeadm.conf` *|

\* The kubelet service startup command can be found by running `service kubelet status` and looking at the "Drop-In:" line.

## tmux

```bash
# Sessions
tmux new -s <name> # Create a new named session
<ctrl-b> d # disconnect from session
tmux ls # View list of sessions
tmux attach -t <name> 
tmux kill-session -t <name>

# Windows
<ctrl+b> c # Create a new window
<ctrl+b> <name> # Switch to a window: `
<ctrl+b> , # Rename a window
exit # Close the current window

# Panes
<ctrl+b> % # to create a new vertical pane to the right
<ctrl+b> <- # (left arrow) to move to the left pane and right arrow to the right
<ctrl+b> <double quote> # Create a new horizontal pane downward
exit # to close a pane

# Scrolling
<ctrl+b> [
# or
<ctrl+b> <PgUp>
```