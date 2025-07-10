# ConfigMaps

## Imperatively

As command arguments:

`kubectl create configmap <configmap-name> --from-literal=<key>=<value>`  
`kubectl create configmap app-config --from-literal=APP_COLOR=blue`

To specify more than one set of KVPs simply pass `--from-literal=` again.

From a file:

`kubectl create configmap app-config --from-file=config-file.properties`  
The key will be the file name and the value will be the base64 encoded file.

## Declaratively

```yaml
apiVersion: v1
kind: ConfigMap
metadata: 
  name: app-config
data: 
  APP_COLOR: blue
  APP_MODE: prod
```

## ConfigMap in Pods

Inject a single env var from a config map:
```yaml
spec:
  containers:
  - name: simple-web-app
    image: ubuntu
    env:
    - name: APP_COLOR
      valueFrom: 
        secretKeyRef:
          name: app-config
          key: APP_COLOR
```

Inject all of the config map vars from a config map.
```yaml
spec:
  containers:
  - name: simple-web-app
    image: ubuntu
    envFrom: 
      - configMapRef:
          name: app-config 
```

