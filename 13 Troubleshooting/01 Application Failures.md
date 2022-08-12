# Application Failures

## Check Accessibility

Service:  User --> web-service --> web pod --> db-service --> db pod

- Check if web server is available via curl:  
`curl http://web-service-ip:node-port`
- Check if the services has discovered `Endpoints` for the pod:  
`kubectl describe service web-service`  
Look for Endpoints, if there are none, then check the service to pod discovery by comparing selectors configured on pod with those on the service.
- Check the pod to make sure it is in a running state:  
`kubectl get po <pod>` 
  - Check if pod is running or not
  - Check number of restarts
  - Check events related to pod using `k describe pod <pod>`
  - Check logs using `k logs <pod>`  May need to follow logs with `-f`
  - Logs reflect current running pod, also check previous logs:  
  `k logs <pod> --previous`
- Check the status of the DB service as before with web service
- Check the DB pod itself as we did with the web pod


