### RBAC-related recipes

[Allow all authenticated users to list, get and watch all StorageClass resources](#rbac1)

#### Recipe/RBAC/1: Allow all authenticated users to list, get and watch all StorageClass resources<a name="rbac1"></a>
:information_source: `StorageClass` is a cluster-scope resource type. `StorageClass` instances are referenced by developers in their namespace-scoped `PersistentVolumeClaim` resources in order to dynamically provision and attach persistent storage to their pods. You may decide to allow all authenticated users to list all `StorageClass` resources in the cluster, no matter in which namespace the users are allowed to work.

:page_with_curl: ClusterRole *storageclass:reader*

    kind: ClusterRole
    apiVersion: rbac.authorization.k8s.io/v1beta1
    metadata:
      name: storageclass:reader
    rules:
    - apiGroups: ["storage.k8s.io"]
      resources: ["storageclasses"]
      verbs: ["get", "watch", "list"]
  
:page_with_curl: ClusterRoleBinding *storageclass:reader*
  
    kind: ClusterRoleBinding
    apiVersion: rbac.authorization.k8s.io/v1beta1
    metadata:
      name: storageclass:reader
    roleRef:
      apiGroup: rbac.authorization.k8s.io
      kind: ClusterRole
      name: storageclass:reader
    subjects:  
    - kind: Group
      name: system:authenticated
      apiGroup: rbac.authorization.k8s.io

:hotsprings: tested against Kubernetes 1.12

