# Security Primitives

## Host Security 

This module doesn't cover host security but does give the general advice that the host must be secured or none of the rest of this matters.  

General recommendations for host security:

1. Root disabled
2. Password based access disabled
3. Only use SSH Key access.

## kube-apiserver

The kube-apiserver is the first line of defense.

### Authentication

- Files - Username and Password
- Files - Username and tokens
- Certificates
- External Authentication Providers, such as LDAP
- Service Accounts (for machine / system access)

### Authorization

- RBAC Authorization
- ABAC Authorization (attribute based access control)
- Node Authorization
- Webhook Mode

### Network Policies

By default, all pods can talk to all other pods in the cluster but this can be controlled via Network Policies (and a service mesh, but he didn't say that)

