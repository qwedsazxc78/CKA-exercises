# Cluster Architecture, Installation & Configuration - 25%

## Cover Range

* Manage role based access control (RBAC)
* Use Kubeadm to install a basic cluster
* Manage a highly-available Kubernetes cluster
* Provision underlying infrastructure to deploy a Kubernetes cluster
* Perform a version upgrade on a Kubernetes cluster using Kubeadm
* Implement etcd backup and restore

## Perform a version upgrade on a Kubernetes cluster using Kubeadm

### Questions

* Upgrade the current version of kubernetes from 1.23.0 to 1.24.0 by the kubeadm utility. There are a control plane and a work node (node01) in this cluster.

<details><summary>Solution</summary><p>

### Concept

From Kubernetes doc link - https://kubernetes.io/docs/tasks/administer-cluster/kubeadm/kubeadm-upgrade/

### Step

Control plane

* Run upgrade command in control plane

```bash
export master=controlplane
export version=1.24.0

# upgrade kubeadm kubelet kubectl
apt-mark unhold kubeadm && \
apt-get update && apt-get install -y kubeadm=${version}-00 && \
apt-mark hold kubeadm

kubeadm upgrade plan
sudo kubeadm upgrade apply v${version} -y

kubectl drain ${master} --ignore-daemonsets

apt-mark unhold kubelet kubectl && \
apt-get update && apt-get install -y kubelet=${version}-00 kubectl=${version}-00 && \
apt-mark hold kubelet kubectl

sudo systemctl daemon-reload
sudo systemctl restart kubelet

kubectl uncordon ${master}
```

Data plane

* Unscheduler task on node01
* Ssh to work node - node01
* Run upgrade command in node01
* Scheduler task on node01

Unscheduler task on node01

```bash
# control plane
export node=node01
kubectl drain ${node} --ignore-daemonsets
```

Ssh to work node - node01 and run upgrade command in node01

```bash
# data plane

export version=1.24.0
apt-mark unhold kubeadm && \
apt-get update && apt-get install -y kubeadm=${version}-00 && \
apt-mark hold kubeadm

sudo kubeadm upgrade node

apt-mark unhold kubelet kubectl && \
apt-get update && apt-get install -y kubelet=${version}-00 kubectl=${version}-00 && \
apt-mark hold kubelet kubectl

sudo systemctl daemon-reload
sudo systemctl restart kubelet

exit
```

Scheduler task on node01

```bash
# control plane
export node=node01
kubectl uncordon ${node}
kubectl get nodes
kubectl get pods -o wide
```

</p></details>
