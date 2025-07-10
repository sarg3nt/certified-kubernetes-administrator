# Secrets

## Imperatively

As command arguments:

`kubectl create secret generic <secret-name> --from-literal=<key>=<value>`  
`kubectl create secret generic app-secret --from-literal=DB_User=root --from-literal=DB_Password=password`

To specify more than one set of KVPs simply pass `--from-literal=` again.

From a file:

`kubectl create secret app-secret --from-file=secret-file.properties`  
The key will be the file name and the value will be the base64 encoded file.

## Declaratively

Values in the data field must be base64 encoded, values in the stringData field do not.  
On Linux use base64 to encode values:  
`echo -n 'root' | base64`   
Results: `cm9vdA==`  

```yaml
apiVersion: v1
kind: Secret
metadata: 
  name: app-secret
data: 
  DB_User: cm9vdA==
  DB_Password: cGFzc3dvcmQ=
stringData: 
  DB_User2: fred
```

## Secrets in Pods

Inject a single env var from a secret:
```yaml
spec:
  containers:
  - name: simple-web-app
    image: ubuntu
    env:
    - name: APP_COLOR
      valueFrom: 
        configMapRef:
          name: app-config
          key: APP_COLOR
```

Inject all of the vars from a secret.
```yaml
spec:
  containers:
  - name: simple-web-app
    image: ubuntu
    envFrom: 
      - secretRef:
          name: app-config 
```

As a volume in the pod.  Each key will be a file name with the contents of the file being the value.
```yaml
spec:
  containers:
  - name: simple-web-app
    image: ubuntu
    volumes: 
    - name: app-secret-volume
      secret:
        secretName: app-secret
```

## A note about Secrets!

Remember that secrets encode data in base64 format. Anyone with the base64 encoded secret can easily decode it. As such the secrets can be considered as not very safe.

The concept of safety of the Secrets is a bit confusing in Kubernetes.  
The [kubernetes documentation](https://kubernetes.io/docs/concepts/configuration/secret/) page and a lot of blogs out there refer to secrets as a "safer option" to store sensitive data. They are safer than storing in plain text as they reduce the risk of accidentally exposing passwords and other sensitive data. In my opinion it's not the secret itself that is safe, it is the practices around it. 

Secrets are not encrypted, so it is not safer in that sense. However, some best practices around using secrets make it safer. As in best practices like:

- Not checking-in secret object definition files to source code repositories.
- [Enabling Encryption at Rest](https://kubernetes.io/docs/tasks/administer-cluster/encrypt-data/) for Secrets so they are stored encrypted in ETCD. 

Also the way kubernetes handles secrets. Such as:

- A secret is only sent to a node if a pod on that node requires it.
- Kubelet stores the secret into a tmpfs so that the secret is not written to disk storage.
- Once the Pod that depends on the secret is deleted, kubelet will delete its local copy of the secret data as well.

Read about the protections and risks of using secrets [here](https://kubernetes.io/docs/concepts/configuration/secret/#risks)

Having said that, there are other better ways of handling sensitive data like passwords in Kubernetes, such as using tools like Helm Secrets, HashiCorp Vault. I hope to make a lecture on these in the future.