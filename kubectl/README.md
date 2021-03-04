###

Running a particular image in a pod:
```
kubectl run --image=luksa/kubia --port=8080
```
The `run` command can be use to run a Pod only. Since 1.18 `run` generators are deprecated: https://github.com/kubernetes/kubernetes/pull/68132

Get the name of the node a particular pod is running on:
```
kubectl get pods -o json | jq ".items[0].spec.nodeName"
```

Count the number of Pods running in the current namespace:
```
kubectl get pods -o json | jq '.items | length'
```

#### Processing Pod information with jq
The following commands:
```
kubectl get pods -o json | jq '.items[] | select(.metadata.name | contains("kubia")) | .metadata.name + " is running on " + .spec.nodeName'
```
produces:
```
"kubia is running on gke-mjk-1-default-pool-4f910f33-7cwg"
"kubia-mate is running on gke-mjk-1-default-pool-4f910f33-7cwg"
```
