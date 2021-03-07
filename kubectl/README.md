### Pods

Running a particular image in a pod:
```
kubectl run --image=luksa/kubia --port=8080
```
The `run` command can be use to run a Pod only. Since 1.18 `run` generators are deprecated: https://github.com/kubernetes/kubernetes/pull/68132

Run a pod with labels:
```
kubectl run kubia-mate --image=luksa/kubia --port=8080 --labels=app=kubia,rel=beta
```

Get the name of the node a particular pod is running on:
```
kubectl get pods -o json | jq ".items[0].spec.nodeName"
```

Count the number of Pods running in the current namespace:
```
kubectl get pods -o json | jq '.items | length'
```

Run an NGINX Pod and run a second Pod to query the NGINX for HTML on Pod IP:
```
kubectl run nginx --image=nginx
# Wait for it
POD_IP=$(kubectl get pod nginx -o json | jq -r ".status.podIP")
kubectl run busybox --image=busybox --restart=Never --rm -it -- wget -O- $POD_IP
```
BusyBox is a lightweight Linux image used to run standard Linux commands as containers. More info: https://hub.docker.com/_/busybox

List status of containers in a Pod:
```
kubectl get pod nginx -o json | jq -r '.status.containerStatuses'
```

#### Exposing a manually created Pod
```
kubectl run nginx --image=nginx
# Wait for it
kubectl expose pod nginx --type=LoadBalancer --name nginx-http --port=80 --target-port=80
# To verify
kubectl get svc nginx-http -o json | jq -r '.spec.ports'
```
produces
```
[
  {
    "nodePort": 30059,
    "port": 80,
    "protocol": "TCP",
    "targetPort": 80
  }
]
```
meaning that the connvectivity goes as follows:  
`Internet -> LoadBalancer (port 80) -> NodePort (30059) -> Service Layer -> Pod (port 80) -> Container (port 80)`

#### command vs args
Container image definition uses two instructions to set the entrypoint command:
- ENTRYPOINT should be defined when using the container as an executable.
- CMD should be used as a way of defining default arguments for an ENTRYPOINT command or for executing an ad-hoc command in a container
https://docs.docker.com/engine/reference/builder/#understand-how-cmd-and-entrypoint-interact
For example:
```dockerfile
FROM debian:buster-slim
# ... a lot of various Dockerfile instructions ...
ENTRYPOINT ["/docker-entrypoint.sh"]
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```
will effectively execute on container startup: `/docker-entrypoint.sh nginx -g daemon off;`

When Kubernetes starts a Container, it runs the image's default `Entrypoint` and passes the image's default `Cmd` as arguments.
It is possible to override their values.

To start a Pod that runs an arbitrary command and arbitrary arguments:
```
kubectl run busybox --image=busybox --command -- <cmd> <arg1> ... <argN>
```
To add only custom arguments, skip the `--command` clause:
```
kubectl run busybox --image=busybox -- <arg1> ... <argN>
```

### Services
Render the LoadBalancer IP with port:
```
kubectl get service kubia-http -o json  | jq -r '.status.loadBalancer.ingress[0].ip+":" + (.spec.ports[0].port|tostring)'
```
produces:
```
35.228.232.121:8080
```

#### Labels
Listing labels:
```
kubectl get pods --show-labels
```
produces:
```
NAME         READY   STATUS    RESTARTS   AGE   LABELS
kubia        1/1     Running   0          93m   run=kubia
kubia-mate   1/1     Running   0          46m   run=kubia-mate
kubia-vsc    1/1     Running   0          56s   app=kubia,rel=alpha
```

Listing selected labels as columns in the output
```
kubectl get pods -L run,app,rel
```
produces:
```
NAME         READY   STATUS    RESTARTS   AGE     RUN          APP     REL
kubia        1/1     Running   0          101m    kubia
kubia-mate   1/1     Running   0          54m     kubia-mate
kubia-vsc    1/1     Running   0          9m21s                kubia   alpha
```

Delete Pods with label `run` **and** without label `app`
```
kubectl delete pods -l run,'!app'
```

Change Label Selector on a Service to `app=kubia`:
```
kubectl set selector service kubia-http app=kubia
```

More on labels: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#label

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
The `-r` flag stands for raw output and in this way the result string is devoid of quotation marks.

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
