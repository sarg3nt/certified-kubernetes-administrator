# Commands and Arguments

`command` is equivilent to Docker's `ENTRYPOINT`  
`args` is equivilent to Docker's `CMD`

Example:

```yaml
spec:
  containers:
    - name: ubuntu
      image: ubuntu
      command: ["sleep"]
      args: 
      - "10"
```
Note: I show the two different ways of passing args as arrays in YAML above.  I prefer the second