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

#### List containers of a particular pod with their images
```
kubectl get pod kubia -o json | jq -r '.spec.containers[] | .name + " (" + .image + ")"'
```
produces
```
kubia (luksa/kubia)
```
The `-r` flag stands for raw output and in this way the result string is devoid of brackets.

#### Understanding manifest elements of a particular Kubernetes resource type
Use `explain` command to see the definition of manifest element(s) of a particular Kubernetes resource type:
```
kubectl explain pod.spec.nodeName
```
produces:
```
KIND:     Pod
VERSION:  v1

FIELD:    nodeName <string>

DESCRIPTION:
     NodeName is a request to schedule this pod onto a specific node. If it is
     non-empty, the scheduler simply schedules this pod onto that node, assuming
     that it fits resource requirements.
```
