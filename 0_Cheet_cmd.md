# Cheet Command

## Quick Alias - Refer [cheatsheet](https://kubernetes.io/docs/reference/kubectl/cheatsheet/)

```bash
alias k=kubectl
alias do="--dry-run=client -o yaml"
alias now="--force --grace-period 0"
```

## Kubernetes Resource Short Name

### High Frequency

* configmaps = cm
* namespaces = ns
* nodes = no
* persistentvolumeclaims = pvc
* persistentvolumes = pv
* pods = po
* serviceaccounts = sa
* services = svc
* daemonsets = ds
* deployments = deploy
* replicasets = rs
* cronjobs = cj
* replicasets = rs

### Low Frequency

* storageclasses = sc
* componentstatuses = cs
* endpoints = ep
* events = ev
* limitranges = limits
* replicationcontrollers = rc
* resourcequotas = quota
* customresourcedefinitions = crd, crds
* statefulsets = sts
* horizontalpodautoscalers = hpa
* certificiaterequests = cr, crs
* certificates = cert, certs
* certificatesigningrequests = csr
* ingresses = ing
* networkpolicies = netpol
* podsecuritypolicies = psp
* scheduledscalers = ss
* priorityclasses = pc
