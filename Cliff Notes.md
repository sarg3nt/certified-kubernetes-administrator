

**WARNING:** This file contains my condensed notes of things I needed to memorize for the test so it probably does not contain everything you need to learn.

- [Kubernetes Documentation](#kubernetes-documentation)
- [Test Tips](#test-tips)
- [Learn tmux](#learn-tmux)
  - [Sessions](#sessions)
  - [Windows](#windows)
  - [Panes](#panes)
- [Resources allowed during exam](#resources-allowed-during-exam)
- [Exam Technical Instructions](#exam-technical-instructions)
- [Environment Setup and kubectl](#environment-setup-and-kubectl)
  - [Kubectl Editor](#kubectl-editor)
  - [kubectl Autocompletion and k Alias](#kubectl-autocompletion-and-k-alias)
  - [kubectl](#kubectl)
  - [Default Namespace](#default-namespace)
  - [Executing Command on Running Pods](#executing-command-on-running-pods)
  - [Setting Image of a Running Resource](#setting-image-of-a-running-resource)
  - [Jsonpath](#jsonpath)
- [Etcd Backup](#etcd-backup)
- [Services](#services)
  - [Ports](#ports)
  - [NodePort](#nodeport)
- [Volumes](#volumes)
  - [Persistent Volumes](#persistent-volumes)
  - [Persistent Volume Claim](#persistent-volume-claim)
- [Security Contexts](#security-contexts)
- [Adding a User, Certificate Signing Requests, Roles](#adding-a-user-certificate-signing-requests-roles)
  - [Roles and Role Bindings](#roles-and-role-bindings)
- [DNS](#dns)
- [Networking](#networking)
  - [Network Policy](#network-policy)
  - [Troubleshooting Commands](#troubleshooting-commands)
- [Processes and Services](#processes-and-services)
- [Paths](#paths)

# Kubernetes Documentation

https://kubernetes.io/docs/home/

- Learn what to search for
- Use the sub navigation on the right of the main docs to drill down to what you need
- Search the page for what you need, example `kind: pod`

# Test Tips

- Don't Panic!
- Use imperative commands to create resources or yaml files
  - `k run pod --help`
  - `k create deployment --help`
  - You can type in part of a command and tack `--help` on at any point to look for the rest of it, then up arrow, delete the `--help` and finish the command.
    - `k create deployment my-deployment --image=nginx --help` (enter)
    - View help, find the bits you need then (up arrow)
    - `k create deployment my-deployment --image=nginx --replicas=3`
- Always check the state of a resource after you create it.  
  - First check if it was created correctly with `k get <resource>`
  - Then check its details with either `k describe <resource>` or `k get <resource> -o yaml`
    - `describe` may have details that the YAML may not, for example Service Endpoints can only be seen with `describe`
    - `-o yaml` may have details that `describe` does not, for example `securityContext` of a pod
- Learn `vi`/`vim` or memorize how to replace the `KUBE_EDITOR` with nano, `export KUBE_EDITOR=nano` 
  - Editing:
    - `insert` key to edit
    - Make changes as normal
    - `esc` key to exit to normal mode
  - Normal Mode:  Where you delete lines, save and exit
    - `esc` key to return to normal mode from edit mode
    - `dd` to delete a line
    - `:wq` to write and quit
    - `:q!` to quit without saving changes

# Learn tmux

`tmux` is a terminal utility for creating and managing terminal sessions, windows and panes.

[This](https://www.youtube.com/watch?v=Yl7NFenTgIo&ab_channel=HackerSploit) is a great 12 minute video on using `tmux`

## Sessions

`tmux` sessions are incredibly powerful as they manages processes in the background for you.  You can create a `tmux` session and hop out of it and it will remain running, more importatnly if your `ssh` session is killed, you can hop back on and recconect to the `tmux` session and not loose your work.

A `tmux` session has a window status bar at the bottom of the terminal that shows the list of current windows in that session.  These can be renamed.

- Create a new non named session: `tmux`
- Create a new named session: `tmux new -s <name>`
- To detach from a tmux session: `ctrl-b d`
- To view sessions: `tmux ls`
- To reattach to an existing session: `tmux attach -t <name>` 
- Rename a session: `tmux rename session -t <target> <new-name>`   
  Example: `tmux rename session -t 0 git` would name session 0 as git
- Kill a session: `tmux kill-session -t <name>`

## Windows

Each `tmux` session can have many windows.  The window names are at the bottom of the terminal with an * indicating wwhich one is currently active.  They are initially numbered but can be named as shown below

- Create a new window: `ctrl+b c`
- Switch to a window: `ctrl+b <name>`  
  Note: By default windows are numbered unless you name them.
- Rename a window: `ctrl+b ,` rename window at bottom, press `<enter>`
- `exit` to close the current window

## Panes

Panes are ways of splitting up a current window into multiple terminal areas.  Panes can be vertically and horizontally split.

- `ctrl+b %` to create a new vertical pane to the right
- `ctrl+b <-` (left arrow) to move to the left pane and right arrow to the right
- `ctrl+b "` Create a new horizontal pane downward
- `exit` to close a pane


# Resources allowed during exam

During the exam, candidates may:

- review the Exam content instructions that are presented in the command line terminal
- review Documents installed by the distribution (i.e. /usr/share and its subdirectories)
- use the browser within the VM to access documentation  at: 
https://helm.sh/docs/, https://kubernetes.io/docs/, https://kubernetes.io/blog/ and their subdomains. This includes all available language translations of these pages (e.g. https://kubernetes.io/zh/docs/)
- use the search function provided on https://kubernetes.io/docs/. However, you may only open search results that have a domain matching the ones specified above.

# Exam Technical Instructions

1. Root privileges can be obtained by running 'sudo −i'.
1. You must NOT reboot the base node (hostname node-1). Rebooting the base node will NOT restart your exam environment.
1. Do not stop or tamper with the certerminal process as this will END YOUR EXAM SESSION.
Do not block incoming ports 8080/tcp, 4505/tcp and 4506/tcp. This includes firewall rules that are found within the distribution's default firewall configuration files as well as interactive firewall commands.
1. Use Ctrl+Alt+W instead of Ctrl+W.
5.1 Ctrl+W is a keyboard shortcut that will close the current tab in Google Chrome.
1. The Terminal (Terminal Emulator Application) is a Linux Terminal; to copy & paste within the Linux Terminal you need to use LINUX shortcuts:
Copy  = Ctrl+SHIFT+C (inside the terminal)
Paste = Ctrl+SHIFT+V (inside the terminal)
OR Use the Right Click Context Menu and select Copy or Paste
1. For security reasons, the INSERT  key is prohibited within the Remote Desktop. 
Candidates can Type i to switch into insert mode so that you can start editing the file. 
Once you're done, press the escape key Esc to get out of insert mode and back to command mode.
1. Installation of services and applications included in this exam may require modification of system security policies to successfully complete.

# Environment Setup and kubectl

In the docs search for `kubectl cheat sheet` and select [kubectl Cheat Sheet](https://kubernetes.io/docs/reference/kubectl/cheatsheet/)

Everything in this section is in the Kubectl Cheat Sheet

## Kubectl Editor

First, make sure nano is installed, if so . . .  
`export KUBE_EDITOR=nano` 

## kubectl Autocompletion and k Alias

The CKA and CKAD instructions indicate that `k` and autocompletion are already installed.  Leaving instructions here in case they are not

First thing in the cheat sheet, I wouldn't bother memorizing this, just know how to get there and what it looks like.
```bash
source <(kubectl completion bash) # setup autocomplete in bash into the current shell, bash-completion package should be installed first.
echo "source <(kubectl completion bash)" >> ~/.bashrc # add autocomplete permanently to your bash shell.
alias k=kubectl
complete -o default -F __start_kubectl k
```

## kubectl 

- Options valid for all kubectl commands can bee seen with `k options`
- To use a different Kube Config file: `k --kubeconfig=<file-path>`
- To see all k8s APIs / Resources `k api-resources`, learn their short names.
- When createing resources like Roles with `kubectl create` you CAN use a resources short name, like `pv` instead of `Persistentvolume`   
Example: `k create role pv-reader --verb=get,list --resource=pv`

## Default Namespace

This is also in the `kubectl cheat sheet` docs but it's best to memorize it

Set default namespace 
```bash
k config set-context --current --namespace=<namespace-name>
```

## Executing Command on Running Pods

Execute a command in a running pod quickly without interactively executing into it

```bash
k exec mypod -- <command to run>
k exec mypod -- cat /log/app.log
```

## Setting Image of a Running Resource

```bash
k set image <rsource-type>/<resource> <container-name>=<image>
k set image deployment/nginx nginx=nginx:1.9.1
```

## Jsonpath

- Jsonpath examples are in the kubectl cheat sheet as well
- When looking at json output, pipe it to jq to make it easier to read.
`k get nodes -o json | jq`
- Use jq to list paths:
`k get nodes -o json | jq -c 'paths'`  
`k get nodes -o json | jq -c 'paths' | grep -i volumesinuse`



# Etcd Backup

In the docs search for `etcdctl backup` and click on `Operating etcd clusters for Kubernetes` in the right hand navigation look for `Backing up an etcd cluster` --> [Snapshot using etcdctl options](https://kubernetes.io/docs/tasks/administer-cluster/configure-upgrade-etcd/#snapshot-using-etcdctl-options)

Look for this command:  
```bash
ETCDCTL_API=3 etcdctl --endpoints=https://127.0.0.1:2379 \
  --cacert=<trusted-ca-file> --cert=<cert-file> --key=<key-file> \
  snapshot save <backup-file-location>
```

Typically the certs can be found in `/etc/kubernetes/pki/etcd`, if they are not then look at the etcd pod speck in `/etc/kubernetes/manifests` or by describing the pod.



# Services

Although you can use `k create service` it is better to use:  
`k expose <resource> <resource-name> --type=NodePort --port=80 --name=ingress --dry-run=client -o yaml > service.yaml`  
as this will create the right selector for you.

Once created always check the service with:  
`k get svc <svc-name>`  
and check the `Endpoints:` is populated with the ip of the target pod, if so then the service has bound to the resource.

## Ports
`--port` is optional and will be copied from the resource being exposed if it is stated there, otherwise will need to state.  
`--targetPort` is optional and is only needed if it is different from `--port`

## NodePort
To expose as a nodePort use `k expose <thing> --type=NodePort` but you cannot give the nodePort value with `k expose`, it will assign one randomly.  If you need to state a specific port then pipe the YAML to a file and edit it adding `nodePort: <port>` in the `ports:` section.



# Volumes

In the docs search for `volumes` and click on [Volumes](https://kubernetes.io/docs/concepts/storage/volumes/)  
Use the right side navigation to find the different volume types, such as `hostPath`, `emptyDir`, etc

## Persistent Volumes

Persistent Volumes specs can be found in the docs in the Volumes page and in the Persistent Volumes page.

Volume types are listed in the Persistent Volumes docs but they link to the Volumes docs. 
You need to know how to take the volume definition from a pod and insert it into a PV.  

Example: `hostPath`.  The pod spec for a `hostPath` volume looks like this.  

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: test-pd
spec:
  containers:
  - image: k8s.gcr.io/test-webserver
    name: test-container
    volumeMounts:
    - mountPath: /test-pd
      name: test-volume
  volumes:
  - name: test-volume
    # This bit goes into the PV
    hostPath:
      path: /data
      type: Directory # optional
```

And a `PersistentVolume` looks like this.  
See how we take the `volumes` --> `hostPath` section and add it on to the `PersistentVolume` --> `spec` section.

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: my-pv
spec:
  capacity:
    storage: 5Gi
  volumeMode: Filesystem # optional
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Recycle # optional
  hostPath:
    path: '/path/to/dir'
```

## Persistent Volume Claim

Search for `pvc` in the docs.  The PVC docs are in the same Persistent Volume page as PVs are.  
Use the right side sub navigation to look for `PersistentVolumeClaims` for the spec.

To add a PVC to a pod, look for `Claims As Volumes` in the sub navigation just below `PersistentVolumeClaims`


# Security Contexts

Search for `security context` in the docs and click on [Configure a Security Context for a Pod or Container](https://kubernetes.io/docs/tasks/configure-pod-container/security-context/)

A security context is used to set several security related capabilities including:

-  runAsUser
-  runAsGroup
-  fsGroup
-  allowPrivilegeEscalation
-  readOnlyRootFilesystem
-  seccompProfile
-  seLinuxOptions, i.e. Assign SELinux labels to a Container
-  capabilities
   -  add NET_ADMIN, SYS_TIME, etc.

These can be set at the pod or container level and container level settings override pod level.  In the example below the container would have `runAsUser` set to `2000`

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: security-context-demo-2
spec:
  securityContext:
    runAsUser: 1000
  containers:
  - name: sec-ctx-demo-2
    image: gcr.io/google-samples/node-hello:1.0
    securityContext:
      runAsUser: 2000
      allowPrivilegeEscalation: false
      capabilities:
        add: ["NET_ADMIN", "SYS_TIME"]
```

# Adding a User, Certificate Signing Requests, Roles

To add a user we create a Certificate Signing Request  using the users certificate.  
Remember that the users name is encoded in their certificate, we don't provide it to Kubernetes any other way.

In the docs search for `csr` and select "Certificate Signing Requests"  
In the right hand sub navigation click on [Normal user](https://kubernetes.io/docs/reference/access-authn-authz/certificate-signing-requests/#normal-user)  
Follow the instructions there, here's the outline:

1. If not given, create a private key and certificate, otherwise use the cert given
2. Create the CertificateSigningRequest 
3. Approve certificate signing request (user is now "created")
4. Get the certificate (if instructed and you need to auth with user via kubectl, otherwise skip)
5. Create Role and RoleBinding (if instructed)
   1. Get and describe to make sure they created correctly and are in the correct name space
6. Add to kubeconfig (if instructed)

These are all in the "Normal user" section mentioned above in the order shown here


## Roles and Role Bindings

Instructions are also in the Certificate Signing Requests page, look for "Create Role and RoleBinding" which is step 5 from above.

**Important:** Remember to read the questions carefully and make sure you creating the correct resource type, Roles vs ClusterRoles and RoleBindings vs ClusterRoleBindings

If instructed to create role and rolebinding check if the user can perform the actions required:  
```bash
# Normal User
k auth can-i <verb> <resource> --as <user> [-n <namespace>]
k auth can-i create pods --as john -n development

# Service Accounts
### Memorize this!
# The --as=system:. . .  is in the docs in the Authorization Overview page
k auth can-i <verb> <resource> --as=system:serviceaccount:<namespace>:<serviceaccountname> [-n <namespace>]
k auth can-i get pods --as=system:serviceaccount:development:my-dev-sa -n development
```

# DNS

Pod and Service DNS can be found in the docs by searching for `dns` and selecting [DNS for Services and Pods](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/#a-aaaa-records)

Notes:

```bash
# Service FQDN is typically
<svc>.<namespace>.svc.cluster.local
my-service.default.svc.cluster.local

# Pod FQDN is typically
<pod-ip-dashed>.<namespace>.pod.cluster.local
10-5-2-7.default.pod.cluster.local
```



# Networking

## Network Policy

Search for `network policy` in the Kubernetes docs and select [Network Policies](https://kubernetes.io/docs/concepts/services-networking/network-policies/)

This is the example from the docs but I've added some notes below
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: test-network-policy
  namespace: default
spec:
  podSelector:
    matchLabels:
      role: db
  policyTypes:
    - Ingress
    - Egress
  ingress:
    # The from field can be as complex or simple as you need it to be.
    # Example: If you want to allow all traffic from port 80, remove the from section 
    # and just have the ports section.
    # These are restrictions so remove what is not needed, do not try to add wildcards to cidr for example.
    - from:
        - ipBlock:
            cidr: 172.17.0.0/16
            except:
              - 172.17.1.0/24
        - namespaceSelector:
            matchLabels:
              project: myproject
        - podSelector:
            matchLabels:
              role: frontend
      ports:
        - protocol: TCP
          port: 6379
  egress:
    - to:
        - ipBlock:
            cidr: 10.0.0.0/24
      ports:
        - protocol: TCP
          port: 5978

```

## Troubleshooting Commands

```bash
# nslookup
k run busybox --image=busybox -- sleep 5000
k exec busybox -- nslookup <endpoint>>
# or, this will run the container allow you inside then remove the container when you exit
k run busybox --image=busybox --rm -it -- sh
# Inside the running container
> nmap <endpoint>

# curl
k run curl --image=alpine/curl -- sleep 5000
k exec curl -- curl http://<endpoint>

# Logs for a service
journalctl -u <service-name>

# To see what ports are open and how many connections they have do 
netstat -plnt

# List all interfaces
ip link
# Get IP
ip a
# find default gateway
ip route
```



# Processes and Services

```bash
# Find what options a service is running with
ps -aux | grep kubelet | grep network
```



# Paths

| Item | Path |
| ----------- | ----------- |
| PKI Certs | `/etc/kubernetes/pki` |
| Static Pod manifests | `/etc/kubernetes/manifests` |
| CNI Bin | `/opt/cni/bin/` |
| CNI config | `/etc/cni/net.d/` |