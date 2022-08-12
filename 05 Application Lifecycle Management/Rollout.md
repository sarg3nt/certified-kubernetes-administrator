# Rollouts

View the status of a rollout with  
`kubectl rollout status deployment/my-deployment`

View the history of a rollout with
`kubectl rollout history deployment/my-deployment`

# Deployment Strategy

`stratagyType`
- Recreate: Destroy all at once then bring up new ones.  Application goes down
- Rolling Update: Bring pod down and replace it one at a time (or whatever burst is set to).  Application does not go down

Describe the deployment and look for `StrategyType` to determine the strategy that is being used for rollouts

## Upgrade

- Edit the source yaml and use `kubectl apply -f my-deployment.yaml` to upgrade the app.
- Can use `kubectl set image deployment/my-deployment <container-name> =nginx:1.9.1` to directly change the image being run.  Note: this does not change the source yaml file.

## Undo Rollout

Upgrading a deployment creates a new replicaset, the old replicaset has its replicas set to 0

To undo a rollout
`kubectl rollout undo deployment/my-deployment`
This sets the new replicaset replicas to 0 and the old one to the desired amount.
Use `kubectl get replicasets` to view them
 