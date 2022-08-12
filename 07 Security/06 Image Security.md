# Image Security

The `image` filed in a pod spec adheres to the published Docker spec.  An image of `image:nginx` auto expands to `<url>/<user>/<imagename>:<version>` or `image:docker.io/library/nginx:latest` for our example here.  If no url is given then docker.io is assumed, if no user is given then library is assumed, if no version is given the latest is assumed.

To use a private registry that requires authentication in k8s you must first create a secret that holds the authentication information.

```bash
kubectl create secret docker-registry regcred \
  --docker-server= \
  --docker-username= \
  --docker-password= \
  --docker-email=
```
`docker-registry` is a built in secret type in k8s that was built to store Docker credentials.

We then state the secret in the pod definition

```yaml
apiVersion: v1
kind: Pod
metadata:
    name: my-pod
spec:
  containers:
  - name: my-pod
    image: my-registry.io/my-pod
  imagePullSecrets:
  - name: regcred
```