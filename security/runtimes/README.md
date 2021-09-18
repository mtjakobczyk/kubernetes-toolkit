### Secure Runtimes

Inspect the runtime in use for a container:
```bash
POD_NAME=simple
CONTAINER_NAME=nginx
CONTAINER_ID=$(k get pod ${POD_NAME} -o=jsonpath="{ .status.containerStatuses[?(@.name==\"${CONTAINER_NAME}\")].containerID }")
CONTAINER_ID=${CONTAINER_ID:13}
echo $CONTAINER_ID
sudo crictl inspect ${CONTAINER_ID} | jq -r '.info.runtimeType'
```
Outputs `io.containerd.runc.v2` or `io.containerd.runsc.v1` (in case the gVisor's `runsc` is used).
