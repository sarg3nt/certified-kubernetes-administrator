# Authentication

## Accounts

Kubernetes does not manage user accounts, it relies on external files or services to do this.  It does manage service accounts, which are used mainly for "bots" / application access.  Service accounts will be discussed later.  
This section is for the authentication of admins and developers access to the k8s cluster.

kube-api server supports the following authentication types

- Static password file
- Static token file
- Certificates
- Identity Services

## Auth Mechanisms - Basic

### user-details.csv

```bash
# Username, pwd, uid, groups(opt)
pasword123,user1,u0001,group1
pasword123,user2,u0003,group2
...
pasword123,userX,u000X,groupX
```
Pass this file to the kube-api server config so it knows who to authenticate:  
`--basic-auth-file=user-details.csv`

Then to authenticate, specify the user and password like this:  
`curl -v -k https://master-node:6443/api/v1/pods -u "user1:password123"`  

### user-token-details.csv

```bash
# Token, pwd, uid, groups(opt)
KpjCVb17r..,user10,u0010,group1
rjx3Vb17r..,user11,u0011,group2
...
V2yXVb17r..,userX,u000X,groupX
```
Pass this file to the kube-api server config so it knows who to authenticate:  
`--token-auth-file=user-token-details.csv`

Then to authenticate, specify the authorization bearer token:  
`curl -v -k https://master-node:6443/api/v1/pods --header "Authorization: Bearer KpjCVb17r.."`

## Note

- This is not a recommended authentication mechanism
- Consider volume mount while providing the auth file in a kubeadm setup
- Setup Role Based Authorization for the new users


# Article on Setting up Basic Authentication

This was taken directly from the course and formatted in markdown.

**Setup basic authentication on Kubernetes (Deprecated in 1.19)**
> Note: This is not recommended in a production environment. This is only for learning purposes. Also note that this approach is deprecated in Kubernetes version 1.19 and is no longer available in later releases

Follow the below instructions to configure basic authentication in a kubeadm setup.

Create a file with user details locally at `/tmp/users/user-details.csv`

```bash
# User File Contents
password123,user1,u0001
password123,user2,u0002
password123,user3,u0003
password123,user4,u0004
password123,user5,u0005
```

Edit the kube-apiserver static pod configured by kubeadm to pass in the user details. The file is located at `/etc/kubernetes/manifests/kube-apiserver.yaml`

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: kube-apiserver
  namespace: kube-system
spec:
  containers:
  - command:
    - kube-apiserver
      <content-hidden>
    image: k8s.gcr.io/kube-apiserver-amd64:v1.11.3
    name: kube-apiserver
    volumeMounts:
    - mountPath: /tmp/users
      name: usr-details
      readOnly: true
  volumes:
  - hostPath:
      path: /tmp/users
      type: DirectoryOrCreate
    name: usr-details
```

Modify the kube-apiserver startup options to include the basic-auth file

```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  name: kube-apiserver
  namespace: kube-system
spec:
  containers:
  - command:
    - kube-apiserver
    - --authorization-mode=Node,RBAC
      <content-hidden>
    - --basic-auth-file=/tmp/users/user-details.csv
```

Create the necessary roles and role bindings for these users:

```yaml
---
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  namespace: default
  name: pod-reader
rules:
- apiGroups: [""] # "" indicates the core API group
  resources: ["pods"]
  verbs: ["get", "watch", "list"]
 
---
# This role binding allows "jane" to read pods in the "default" namespace.
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: read-pods
  namespace: default
subjects:
- kind: User
  name: user1 # Name is case sensitive
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role #this must be Role or ClusterRole
  name: pod-reader # this must match the name of the Role or ClusterRole you wish to bind to
  apiGroup: rbac.authorization.k8s.io
```

Once created, you may authenticate into the kube-api server using the users credentials

`curl -v -k https://localhost:6443/api/v1/pods -u "user1:password123"`