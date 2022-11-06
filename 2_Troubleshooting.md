# Troubleshooting - 30%

## Cover Range

- Evaluate cluster and node logging
- Understand how to monitor applications
- Manage container stdout & stderr logs
- Troubleshoot application failure
- Troubleshoot cluster component failure
- Troubleshoot networking

## Troubleshoot cluster component failure

### Question

* A kubeconfig file called admin.kubeconfig has been created in ~/admin.kubeconfig. There is something wrong with the configuration. Troubleshoot and fix it.

<details><summary>Solution</summary>
<p>

#### Concept

* Check admin.kubeconfig contents and figure out possible error
* Keep remember server url - https://controlplane:6443
* Easy way: crazy number 666 + https 443 -> 6443

#### Step

Run command to get structure

```bash
cat ~/admin.kubeconfig
```
```yaml
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: LS0tLS1CRUdJTiBDRVJUSUZJ...
    server: https://controlplane:4380
  name: kubernetes
contexts:
- context:
    cluster: kubernetes
    user: kubernetes-admin
  name: kubernetes-admin@kubernetes
current-context: kubernetes-admin@kubernetes
kind: Config
preferences: {}
users:
- name: kubernetes-admin
  user:
    client-certificate-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0...
    client-key-data: LS0tLS1CRUdJTiBSU0Eg...
```

Observe the yaml structure and fix the port on server.
You can use sed to replace specific word to you.

```bash
sed -i 's|https://controlplane:4380|https://controlplane:6443|g' /root/CKA/admin.kubeconfig
cat ~/admin.kubeconfig
```
```yaml
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: LS0tLS1CRUdJTiBDRVJUSUZJ...
    server: https://controlplane:6443
  name: kubernetes
contexts:
- context:
    cluster: kubernetes
    user: kubernetes-admin
  name: kubernetes-admin@kubernetes
current-context: kubernetes-admin@kubernetes
kind: Config
preferences: {}
users:
- name: kubernetes-admin
  user:
    client-certificate-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0...
    client-key-data: LS0tLS1CRUdJTiBSU0Eg...
```

</p>
</details>
