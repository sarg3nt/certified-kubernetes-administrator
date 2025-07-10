# Security Contexts

Security settings such as user and adding or removing linux capabilities can be configured at the container level or the pod level. Pod level settings will carry into all the containers. If configured in both places the container settings will override the pod settings.

```yaml
apiVersion: v1
kind: Pod
metadata:
    name: my-pod
spec:
  containers:
  - name: ubuntu
    image: ubuntu
    command: ["sleep", "3600"]
    # The securityContext section can also go under spec to set them for the pod but note that capabilities can only be set at the container level.
    securityContext: 
      runAsUser: 1000
      capabilities:
        add: ["MAC_ADMIN"]
```