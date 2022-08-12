# API Groups

The API can be accessed at the the IP of one of the masters with a port of 6443.   
`curl https://kube-master:6443/version` would show us the version of the API  
`curl https://kube-master:6443/api/v1/pods` would show us a list of pods.  

## API Root Paths

- **/version**
- **/api**   This is the core group of APIs
  - v1
    - namespace
    - pod
    - rc
    - events
    - endpoints
    - nodes
    - bindings
    - PV
    - PVC
    - configmaps
    - secrets
    - services
- **/apis**  This is the named group of APIs. Going forward all new features will be under apis
  - /apps
    - /v1
      - /deployments (this level is known as resources)
        - list       (this level is know as verbs)
        - get
        - create
        - delete
        - update
        - watch
      - /replicasets
      - /statefulsets
  - /extensions
  - /networking.k8s.io
    - /v1
      - /networkpolicies
  - /storage.k8s.io
  - /authentication.k8s.io
  - /certificates.k8s.io
    - /v1
      - /certificatesigningrequests
- **/metrics**
- **/healthz**
- **/logs**

To talk to the API via curl we must send our auth certs but another option is to use and authenticate with `kubectl proxy`, we can then curl `http://localhost:8001 -k` and we will be talking through the proxy and will be authenticated.

**NOTE: kube proxy != kubectl proxy**  
- **kube proxy** is used to facilitate communication between pods and services on different nodes in the cluster.
- **kubectl proxy** is an http proxy that kubectl starts up to grant the local user direct access to the kubernetes API.