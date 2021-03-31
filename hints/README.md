### Services
A `Service` can point to:
- a **set of pods** (using label selector)
  - traffic is load balanced and routed to one of the pods
  - an internal IP (called `ClusterIP`) is allocated for the Service
    - this IP is used to load balance the requests by dispatching them to one of the pods
  - Kubernetes dynamically adjusts the list of pods in the Service-corresponding `Endpoints` object
    - label selector is used to select the appropriate pods
    - only pods with a successful readiness probe are included in the list
  - uses kube-proxy: https://kubernetes.io/docs/concepts/services-networking/service/#virtual-ips-and-service-proxies
- a **static IP** (external to the cluster)
  - traffic is routed to the single endpoint based on the static IP
  - no `ClusterIP` in use (*"headless service"* - set `spec.clusterIP: None`) 
  - no label selector in use
  - https://kubernetes.io/docs/concepts/services-networking/service/#headless-services
- a **DNS name**
  - traffic is routed to the single endpoint based on the DNS name
  - no label selector in use
  - https://kubernetes.io/docs/concepts/services-networking/service/#externalname

For most Kubernetes deployments (including GKE), `kube-proxy` is working in the **iptables mode**:
1. For each Service, it installs iptables rules, which capture traffic to the Service's clusterIP and port 
2. For each Endpoint object, it installs iptables rules which select a backend Pod.
3. It redirect the incoming traffic to one of the Service's backend sets. 
https://kubernetes.io/docs/concepts/services-networking/service/#proxy-mode-iptables

If kube-proxy is running in iptables mode and the first Pod that's selected does not respond, the connection fails. This is why it is important to rely on readiness probes.
