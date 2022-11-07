# Workloads & Scheduling - 15%

## Cover Range

* Understand deployments and how to perform rolling update and rollbacks
* Use ConfigMaps and Secrets to configure applications
* Know how to scale applications
* Understand the primitives used to create robust, self-healing, application deployments
* Understand how resource limits can affect Pod scheduling
* Awareness of manifest management and common templating tools

## Understand the primitives used to create robust, self-healing, application deployments

### Question

* Create a new deployment called nginx-deploy, with image nginx:1.16 and 1 replica. Next upgrade the deployment to version 1.17 using rolling update.

<details><summary>Solution</summary>
<p>

#### Concept

* create deployment and maintain version

#### Step

Run command to create and set version

```bash
k create deploy nginx-deploy --image=nginx:1.16 --replicas=1
k set image deployment.apps/nginx-deploy nginx=nginx:1.17
k get deployment.apps/nginx-deploy
```

</p>
</details>

## Use ConfigMaps and Secrets to configure applications

### Question

* Create a pod called secret-1234 in the admin namespace using the busybox image. The container within the pod should be called secret-admin and should sleep for 4800 seconds.
* The container should mount a read-only secret volume called secret-volume at the path /etc/secret-volume. The secret being mounted has already been created for you and is called dotfile-secret.

<details><summary>Solution</summary>
<p>

#### Concept

* create pod with secret environment.

#### Step

Create yaml to create pod

```bash
cat <<EOF >pod.yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: secret-1234
  name: secret-1234
  namespace: admin
spec:
  volumes:
  - name: secret-volume
    secret:
      secretName: dotfile-secret
  containers:
  - image: busybox
    name: secret-admin
    command:
    - "sh"
    - "-c"
    - "sleep 4800"
    volumeMounts:
    - name: secret-volume
      mountPath: /etc/secret-volume
      readOnly: true
EOF
kubectl apply -f pod.yaml
```

</p>
</details>

## Awareness of manifest management and common templating tools

### Question

* Print the names of all deployments in the admin namespace in the following format: DEPLOYMENT CONTAINER_IMAGE READY_REPLICAS NAMESPACE "deployment name" "container image used" "ready replica count" "Namespace"
* The data should be sorted by the increasing order of the deployment name.
* output to /opt/admin_data

```bash
DEPLOYMENT CONTAINER_IMAGE READY_REPLICAS NAMESPACE
deploy1     redis:alpine         1          admin
```

<details><summary>Solution</summary>
<p>

#### Concept

* Check deployment yaml format and use -o query by cli.
* Note to add --sort-by args in output
* Output result to file

#### Step

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
