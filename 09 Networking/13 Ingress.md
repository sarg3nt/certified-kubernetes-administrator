# Ingress

In k8s version 1.20+ we can create an Ingress resource from the imperative way like this:

Format - `kubectl create ingress <ingress-name> --rule="host/path=service:port"`

Example - `kubectl create ingress ingress-test --rule="wear.my-online-store.com/wear*=wear-service:80"`

Use help for more examples: `k create ingress -h`  
You can pass multiple `--rule` args

`k describe ingress <ingress-name>` will show if the ingress has mapped to the service endpoints.

Find more information and examples in the below reference link:-

https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#-em-ingress-em-

References:-

https://kubernetes.io/docs/concepts/services-networking/ingress

https://kubernetes.io/docs/concepts/services-networking/ingress/#path-types

## Common Annotations

When using sub paths, it is common to want to rewrite `/mypath/` to `/` inside the pod.  Do this with the following annotation:  
`nginx.ingress.kubernetes.io/rewrite-target: /`

If not doing ssl redirect then:  
`nginx.ingress.kubernetes.io/ssl-redirect: "false"`  
Not having the above can cause the too many redirects error.