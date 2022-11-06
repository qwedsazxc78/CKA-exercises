# Storage - 10%

## Cover Range

* Understand storage classes, persistent volumes
* Understand volume mode, access modes and reclaim policies for volumes
* Understand persistent volume claims primitive
* Know how to configure applications with persistent storage

## Understand the primitives used to create robust, self-healing, application deployments

### Question

* A new deployment called alpha-mysql has been deployed in the alpha namespace. However, the pods are not running. Troubleshoot and fix the issue.
* The deployment should make use of the persistent volume alpha-pv to be mounted at /var/lib/mysql and should use the environment variable MYSQL_ALLOW_EMPTY_PASSWORD=1 to make use of an empty root password.
* Do not alter the persistent volume.

<details><summary>Solution</summary>
<p>

#### Concept

* check pv, pvc, and deployment status and recognize the error

#### Step

Check persistent volumes and persistent volumes claim

```bash
k get pvc -n alpha -o yaml
k get pv -n alpha -o yaml
```


```yaml
apiVersion: v1
items:
- apiVersion: v1
  kind: PersistentVolume
  metadata:
    annotations:
      kubectl.kubernetes.io/last-applied-configuration: |
        {"apiVersion":"v1","kind":"PersistentVolume","metadata":{"annotations":{},"name":"alpha-pv"},"spec":{"accessModes":["ReadWriteOnce"],"capacity":{"storage":"1Gi"},"hostPath":{"path":"/opt/pv-1"},"storageClassName":"slow"}}
    creationTimestamp: "2022-11-06T03:00:49Z"
    finalizers:
    - kubernetes.io/pv-protection
    name: alpha-pv
    resourceVersion: "1920"
    uid: 73ae4d5b-7c87-41f3-927d-aee495e6c417
  spec:
    accessModes:
    - ReadWriteOnce
    capacity:
      storage: 1Gi
    hostPath:
      path: /opt/pv-1
      type: ""
    persistentVolumeReclaimPolicy: Retain
    storageClassName: slow
    volumeMode: Filesystem
  status:
    phase: Available
kind: List
metadata:
  resourceVersion: ""
  selfLink: ""
```
```yaml
apiVersion: v1
items:
- apiVersion: v1
  kind: PersistentVolume
  metadata:
    annotations:
      kubectl.kubernetes.io/last-applied-configuration: |
        {"apiVersion":"v1","kind":"PersistentVolume","metadata":{"annotations":{},"name":"alpha-pv"},"spec":{"accessModes":["ReadWriteOnce"],"capacity":{"storage":"1Gi"},"hostPath":{"path":"/opt/pv-1"},"storageClassName":"slow"}}
    creationTimestamp: "2022-11-06T03:00:49Z"
    finalizers:
    - kubernetes.io/pv-protection
    name: alpha-pv
    resourceVersion: "1920"
    uid: 73ae4d5b-7c87-41f3-927d-aee495e6c417
  spec:
    accessModes:
    - ReadWriteOnce
    capacity:
      storage: 1Gi
    hostPath:
      path: /opt/pv-1
      type: ""
    persistentVolumeReclaimPolicy: Retain
    storageClassName: slow
    volumeMode: Filesystem
  status:
    phase: Available
kind: List
metadata:
  resourceVersion: ""
  selfLink: ""
```

Create the proposed pvc.yaml and apply the yaml. It should be valid in this deployment.

```bash
cat <<EOF >pvc.yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mysql-alpha-pvc
  namespace: alpha
spec:
  accessModes:
    - ReadWriteOnce
  storageClassName: slow
  resources:
    requests:
      storage: 1Gi
EOF
kubectl apply -f pvc.yaml
```

</p>
</details>
