# Command Memorization

List of commands I am currently memorizing

## Main Memorization List

```bash
export do="--dry-run=client -o yaml" # k create deploy nginx --image=nginx $do
export now="--force --grace-period 0" # k delete pod nginx $now

# Show paths in jq
k get nodes -o json | jq -c 'paths'

## nano coonfig
export KUBE_EDITOR=nano
nano ~/.nanorc
# in the file (inccorect commands will be red, correct will turn green)
set tabsize 2
set tabstospaces

# Kubelet Troubleshooting
ps -aux | grep kubelet

# To see what ports are open and how many connections they have open
netstat -plnt

ip link # interfaces
ip a # IP
ip route # # find default gateway
ps -aux | grep kubelet | grep network # Find what options a service is running with

## yq
yq eval ".user.addresses" user.yaml # Shows the array in array form
yq eval ".user.addresses[]" user.yaml # Flattens the array to KVPs (splat)
yq eval ".user.addresses[1]" user.yaml # Gets the second item in the array
yq eval '.user.orders[] | select(. == "43*")' user.yaml # gets order starting with 43

## Search Terms in Kube docs
# Expose pod information to containers with fieldRef
# Volume types are in the "Volumes" docs
# security context

```

## Refresher

List of commands I moostly have down but should revisit before the test.

```bash
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

## `tmux`

```bash
# Sessions
tmux new -s <name> # Create a new named session
<ctrl-b> d # dissconnect from session
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