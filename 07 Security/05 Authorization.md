# Authorization

## Authorization Mechanisms 

These are called "modes"
- Node
  - Used by the kubelet on each node via the system:node group
- ABAC
  - Users or groups get a set of permissions
  - Difficult to manage
  - A JSON file piped into the Kube API Server grants access.  
  - Any time the JSON file changes the kube API Server must be restarted
- RBAC
  - A role is given a set of permissions
  - Users are associated with one or more roles
  - When a role is updated the users instantly reflect the change.
- Webhook
  - Authorization is managed by an external service like Open Policy Agent
- AlwaysAllow
- AlwaysDeny

The mode is set on the Kube API Server via the `--authorization-mode` parameter.  If no mode is set then `AlwaysAllow` is the default.  You can provide a comma separated list of modes and order matters, first is checked first, etc.  
Example: `--authorization-mode=Node,RBAD,Webhook`

## RBAC

First create the role

`k create role pod-reader --verb=get,list,watch --resource=pods`

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: developer
rules:
- apiGroups: [""] 
  resources: ["pods"]
  verbs: ["list“, "get", “create“, “update“, “delete"]
- apiGroups: [""] 
  resources: [“ConfigMap"]
  verbs: [“create“]
```

Then link the user to the role with a role binding

`k create rolebinding admin --clusterrole=admin --user=user1 --user=user2 --group=group1`

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: devuser-developer-binding
  namespace: default
subjects:
- kind: User
  name: dev-user
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: developer
  apiGroup: rbac.authorization.k8s.io
```

Restrict access to a specific resource, like one or more specific pods.  
Below would grant the developer role access to just the pods named blue and orange.

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: developer
rules:
- apiGroups: [""] 
  resources: ["pods"]
  verbs: ["list“, "get", “create“, “update“, “delete"]
  resourceNames: ["blue", "orange"]
```

Commands
```bash
kubectl get roles
kubectl get rolebindings
kubectl describe role <role-name>
kubectl describe rolebinding <role-binding-name>

# Checking access
kubectl auth can-i create deployments
kubectl auth can-i delete nodes
# Admins can impersonate a user
kubectl auth can-i create deployments --as <user-name> -n <namespace>

# Return count of number of roles in cluster
kubectl get roles -A --no-headers | wc -l
```

# Cluster Roles and RoleBindings

Get a list of resources that are not namespaced
`k api-resources --namespaced=false`

ClusterRole

`k create clusterrole node-admin --verb=create,get,list,update,delete --resource=nodes`


```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: cluster-administrator
rules:
- apiGroups: [""] 
  resources: [“nodes"]
  verbs: ["list“, "get", “create“, “delete"]
```

ClusterRoleBinding

`kubectl create clusterrolebinding node-admin --clusterrole=node-admin --user=user1 --user=user2 --group=group1`

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: cluster-admin-role-binding
subjects:
- kind: User
  name: cluster-admin
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: ClusterRole
  name: cluster-administrator
  apiGroup: rbac.authorization.k8s.io
```

**NOTE:** You can create a cluster role for namespaced resources, such as pods.  This type of role would have the given access to all of that resource across all namespaces.

# Service Accounts

Bots and apps use service accounts.  
Create a Service Account and use it's generated token as an Authorization Bearer like this:  
`curl https://<ip> --insecure --header "Authorization: Bearer <token>`

Creating Service Accounts
```bash
k create serviceaccount <name>
k get serviceaccount

# service accounts create a token that is stored in a secret.
# We can get the name of the secret with
k get sa default -o jsonpath='{.secrets[0].name}'
# and then get the secret by using the output of the above
k get secret $(k get sa default -o jsonpath='{.secrets[0].name}')
# add on the jsonpath to get the token
k get secret $(k get sa default -o jsonpath='{.secrets[0].name}') -o jsonpath='{.data.token}' | base64 -d
# This directly outputs the token.
```

By default kubernetes creates an SA in each namespace called `default`.  The token for the default SA is auto mounted in each pod as a volume at `/var/run/secrets/kubernetes.io/serviceaccount/`  There will be three files `ca.crt`, `namespace` and `token`.  Use the token for Auth Bearer.

The default SA has very limited apabilities, if you need more than that, create a custom SA, give it the privliges it needs and mount it in your pod using `serviceAccountName` like this.

```yaml
apiVersion: v1
kind: pod
metadata:
  name: my-pod
spec:
  containers: 
    - name: my-pod
      image: ubuntu
  serviceAccountName: <my-service-account>
```

**Note:** Set `automountServiceAccountToken:false` under the pod spec if you don't need a token mounted.



