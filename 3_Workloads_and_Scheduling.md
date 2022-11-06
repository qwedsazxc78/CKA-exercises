# Workloads & Scheduling - 15%

## Cover Range

* Understand deployments and how to perform rolling update and rollbacks
* Use ConfigMaps and Secrets to configure applications
* Know how to scale applications
* Understand the primitives used to create robust, self-healing, application deployments
* Understand how resource limits can affect Pod scheduling
* Awareness of manifest management and common templating tools

## Awareness of manifest management and common templating tools

Question:

* Print the names of all deployments in the admin namespace in the following format: DEPLOYMENT CONTAINER_IMAGE READY_REPLICAS NAMESPACE "deployment name" "container image used" "ready replica count" "Namespace"
* The data should be sorted by the increasing order of the deployment name.
* output to /opt/admin_data

```bash
DEPLOYMENT CONTAINER_IMAGE READY_REPLICAS NAMESPACE
deploy1     redis:alpine         1          admin
```

<details><summary>Solution</summary>
<p>

### Concept

* Check deployment yaml format and use -o query by cli.
* Note to add --sort-by args in output
* Output result to file

### Step

Run command to get structure

```bash
kubectl get deploy1 -n admin
```

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  annotations:
    deployment.kubernetes.io/revision: "1"
  creationTimestamp: "2022-11-05T03:11:15Z"
  generation: 1
  labels:
    app: deploy1
  name: deploy1
  namespace: admin
  resourceVersion: "4593"
  uid: eca49176-d964-4537-899c-8d23a867c69a
spec:
  progressDeadlineSeconds: 600
  replicas: 1
  revisionHistoryLimit: 10
  selector:
    matchLabels:
      app: deploy1
  strategy:
    rollingUpdate:
      maxSurge: 25%
      maxUnavailable: 25%
    type: RollingUpdate
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: deploy1
    spec:
      containers:
      - image: redis
        imagePullPolicy: Always
        name: redis
        resources: {}
        terminationMessagePath: /dev/termination-log
        terminationMessagePolicy: File
      dnsPolicy: ClusterFirst
      restartPolicy: Always
      schedulerName: default-scheduler
      securityContext: {}
      terminationGracePeriodSeconds: 30
```

Observe the yaml structure and use --sort-by and -o custom-columns to get target output format.
You can check output data in terminal before output to file.

```bash
kubectl get deploy -n admin --sort-by=.metadata.name \
-o custom-columns=DEPLOYMENT:.metadata.name,CONTAINER_IMAGE:.spec.template.spec.containers[].image,READY_REPLICAS:.status.readyReplicas,NAMESPACE:.metadata.namespace > /opt/admin_data
```

Output data

```bash
cat /opt/admin_data

DEPLOYMENT   CONTAINER_IMAGE   READY_REPLICAS   NAMESPACE
deploy1         redis             1                  admin
deploy2         redis:alpine      1                  admin
deploy3         redis:1.16        1                  admin
deploy4         redis:1.17        1                  admin
deploy5         redis:latest      1                  admin
```

</p>
</details>
