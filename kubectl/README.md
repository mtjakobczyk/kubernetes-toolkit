###

Running a particular image in a pod:
```
kubectl run --image=luksa/kubia --port=8080
```
The `run` command can be use to run a Pod only. Since 1.18 `run` generators are deprecated: https://github.com/kubernetes/kubernetes/pull/68132
