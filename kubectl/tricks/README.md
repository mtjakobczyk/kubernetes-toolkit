### Private Container Registries

#### Recipe/KUBECTL/1 Storing credentials to private container registries
:information_source: When Kubernetes pulls container images from a private container registry, authenticated and authorized access is often required. Deployment descriptors will usually reference a `Secret` that contains the required credentials. To create such secret, run this command:

:computer: Commands (shell)

    kubectl create secret \
        docker-registry artifactory-secret --docker-server=$ARTIFACTORY_URL \
        --docker-username="$ARTIFACTORY_USER" --docker-password="$ARTIFACTORY_PASS" \
        -n $K8S_NAMESPACE

Secret details can be read in such a way:

:computer: Commands (shell)

    kubectl get secret \
        artifactory-secret -n some-namespace \
        -o "jsonpath={.data.\.dockerconfigjson}" | base64 --decode | jq
    
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
