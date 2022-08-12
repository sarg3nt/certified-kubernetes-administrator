# Network Policies

By default, k8s ships with an allow all network policy.

Applying the following would block all traffic to the pod matching the selector

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: db-policy
spec:
 podSelector:
   matchLabels:
     role: db
```

To allow traffic from the api-pod we need add more to the above, like so.

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: db-policy
spec:
 # What pod is this rule going to apply to
 podSelector:
   matchLabels:
     role: db
  # What types of policies will this rule have (ingress, egress)
  policyTypes:
  - Ingress:
  - Egress:
  # The spec for ingress, each ingress will have 'from" and 'ports" fields
  ingress:
  - from:
    - podSelector:
        matchLabels:
          name: api-pod
      # This has no dash, so it is a part of the podSelector rule which then make this an AND operator
      namespaceSelector:
        matchLabels:
          # Note: this is a label, not the metaData.Name field.
          name: prod    
    # To allow an external server, outside the k8s cluster, then use ipBlock
    # This has a dash which starts a new rule, this then is an OR with the rule above.
    - ipBlock: 
        cidr: 192.165.5.10/32
        except: 
          - 192.165.5.11
    ports: 
    - protocol: TCP
      port: 3306
  egress:
  - to:
      #The rest of this supports the same options as shown above in egress, only difference is the 'to" instead of 'from'
```