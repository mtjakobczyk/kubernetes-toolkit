### kubectl tricks

#### Recipe/KUBECTL/1 
:information_source: When Kubernetes pulls container images from a private container registry, authenticated and authorized access is often required. 

:computer: Commands (shell)

    kubectl get secret artifactory-secret -n some-namespace -o "jsonpath={.data.\.dockerconfigjson}" | base64 --decode | jq
    
:dash: Output:
 
```json
{
  "auths": {
    "server.artifactory.intern": {
      "username": "...",
      "password": "...",
      "auth": "..."
    }
  }
}
```

:hotsprings: tested against Kubernetes 1.17
