# Managing State with Deployments

## Deployment Details

This command will generate a deployment

```bash
kubectl create deployment dev-web --image=nginx:1.21
```

To generate YAML file of newly created objects

```bash
kubectl get deployment dev-web -o yaml > dev-web.yaml
```

Sometimes, a JSON output can make it more clear

```bash
kubectl get deployment dev-web -o json > dev-web.json
```

```yaml
apiVersion: v1
items:
- apiVersion: apps/v1
  kind: Deployment
```

## Explaination of Objects

* **apiVersion**: A value of **v1** indicates this object is considered to be a stable resource. In this case, it is not the deployment. It is a reference to the List type.
* **items**: As the previous line is a **List**, this declares the list of items the command is showing
* **-apiVersion**: The dash is a YAML indication of the FIRST item of the list, which declares the **apiVersion** of the object as **apps/v1**.
* **kind**: This is where the type of object to create is declared. The **kind** of the object is **Deployment**.

## Deployment Configuration Metadata

```yaml
# Continue with previous yaml output
metadata:
  annotations:
    deployment.kubernetes.io/revision: "1"
  creationTimestamp: 2024-10-21T13:57:07Z
  generation: 1
  labels:
    app: dev-web
  name: dev-web
  namespace: default
  resourceVersion: "774003"
  uid: d52d3a63-e656-11e7-9319-42010a800003
```

* **annotations**: These values do not configure the object, but are used to store additional information about the object.
* **creationTimestamp**: This is the time the object was created. Notice that this will not change even if the object is updated.
* **generation**: This is the revision of the object. It is incremented every time the object is updated.
* **labels**: Arbitrary key-value pairs that can be attached to the object. These are used to identify and select objects (with `kubectl get` or `kubectl delete`).
* **name**: The name of the object.
* **namespace**: The namespace of the object.
* **resourceVersion**: A value tied to etcd database to help with concurrency of objects.
* **uid**: A unique identifier for the object.

## Deployment Spec

```yaml
# Continue with previous yaml output
spec:
  progressDeadlineSeconds: 600
  replicas: 1
  revisionHistoryLimit: 10
  selector:
    matchLabels:
      app: dev-web
  strategy:
    rollingUpdate:
      maxSurge: 25%
      maxUnavailable: 25%
    type: RollingUpdate
```

* **spec**: This is where the configuration of the object is declared.
* **progressDeadlineSeconds**: This is the amount of time in seconds to wait before giving up on a deployment. Reasons could be quotas, image issues, limit ranges,...
* **replicas**: This is the number of replicas to run. This determines the number of pods to run.
* **revisionHistoryLimit**: This is the number of old replicas to keep. This is used to determine the number of old replicas to keep.
* **selectors**: A collection of values ANDed together. All must be satisfied for the replica to match. Notice that DO NOT create any standalone Pods which match these selectors, as the deployment controller may try to control the resource, leading to issues.
* **matchLabels**: Set-based requirements of the Pod selector.
* **strategy**: This is the strategy to use for updating the deployment. Could be `RollingUpdate` - control how many pods to update at once, or `Recreate` - delete all pods before creating new ones.
* **maxSurge**: Maximum number of Pods over desired number of Pods to create. Can be absolute number or percentage (default: 25%). This creates a certain number of new Pods before deleting old ones, for continued access.
* **maxUnavailable**: A number or percentage of Pods which can be in a state other than **Ready** during the update process (default: 25%).

## Deployment Configuration Pod Template

```yaml
template:
  metadata:
    creationTimestamp: null
    labels:
      app: dev-web
  spec:
    containers:
    - image: nginx:1.17.7-alpine
      imagePullPolicy: IfNotPresent
      name: dev-web
      resources: {}
      terminationMessagePath: /dev/termination-log
      terminationMessagePolicy: File
    dnsPolicy: ClusterFirst
    restartPolicy: Always
    schedulerName: default-scheduler
    securityContext: {}
    terminationGracePeriodSeconds: 30
```

* **template**: Data being passed to the ReplicaSet to determine how to deploy an object (in this case, container).
* **containers**: Keyword indicating that the following items of this identation are for container.
* **image**: The image to use for the container.
* **imagePullPolicy**: Policy settings passed along to the container engine, about when and if an image should be pulled (default: `IfNotPresent`).
* **name**: The name of the container.
* **resources**: By default, empty. This is where resource requests and limits are declared (CPU and memory).
* **terminationMessagePath**: The path to the file containing the termination message.
* **terminationMessagePolicy**: The policy for the termination message. The default value is `File`, which holds the termination method. It could also be set to `FallbackToLogsOnError`, which will use the last chunk of container log if the message file is empty and the container shows an error.
* **dnsPolicy**: Determines if DNS queries should go to `coredns` or, if set to `Default`, use the node's DNS resolution configuration.
* **restartPolicy**: Should the container be restarted if killed? Automatic restarts are part of the typical strength of Kubernetes.
* **scheduleName**: Allows for the use of a custom scheduler, instead of Kubernetes default.
* **securityContext**: Flexible setting to pass one or more security settings, such as SELinux context, AppArmor values, users and UIDs for the containers to use.
* **terminationGracePeriodSeconds**: The amount of time to wait for a `SIGTERM` signal to run until a `SIGKILL` signal is sent to terminate the container.

## Scaling and Rolling Updates

The API server allows for the configurations settings to be updated for most values. There are some immutable values, which maybe different depending on the version of Kubernetes you have deployed.

A common update is to change the number of replicas running. If this number is set to zero, there would be NO containers, but there would still be a ReplicaSet and Deployment. This is the backend process when a Deployment is deleted.

```bash
kubectl scale deploy/dev-web --replicas=4
```

## Deployment Rollbacks

With some of the previous ReplicaSets of a Deployment being kept, you can also roll back to a previous revision by scaling up and down. The number of previous configurations kept is configurable, and has changed from version to version.


```bash
kubectl create deploy ghost --image=ghost
kubectl annotate deployment/ghost kubernetes.io/change-cause="kubectl create deploy ghost --image=ghost"
kubectl get deployments ghost -o yaml
# deployment.kubernetes.io/revision: "1"
# kubernetes.io/change-cause: kubectl create deploy ghost --image=ghost
```

Should an update fail, due to an improper image version, for example, you can roll back the change to a working version with the kubectl rollout undo command (followed by the output):

```bash
kubectl set image deployment/ghost ghost=ghost:09 --all
kubectl get pods
# NAME                    READY  STATUS            RESTARTS  AGE
# ghost-2141819201-tcths  0/1    ImagePullBackOff  0         1m
kubectl rollout undo deployment/ghost ; kubectl get pods
# NAME                    READY  STATUS   RESTARTS  AGE
# ghost-3378155678-eq5i6  1/1    Running  0         7s
```

You can also pause a Deployment, and then resume. See the following two commands:

```bash
kubectl rollout pause deployment/ghost
kubectl rollout resume deployment/ghost
```

## Labels

> Labels are not API objects, they are important tool for cluster administrator. They can be used to select an object based on an arbitrary string key-value pair. Every resources can contain labels in its metadata.

```yaml
....
  labels:
    pod-template-hash: "3378155678"
    run: ghost ....
```

You then could view labels in new columns:

```bash
kubectl get po --show-labels
```

You can also select objects based on labels:

```bash
kubectl get po -l run=ghost
# NAME                    READY  STATUS   RESTARTS  AGE
# ghost-3378155678-eq5i6  1/1    Running  0         10m
```

Or display labels in the column:

```bash
kubectl get pods -L run
# NAME                    READY  STATUS   RESTARTS  AGE  RUN
# ghost-3378155678-eq5i6  1/1    Running  0         10m  ghost
# nginx-3771699605-4v27e  1/1    Running  1         1h   nginx
```

While you typically define labels in pod templates and in the specifications of Deployments, you can also add labels on the fly:

```bash
kubectl label pods ghost-3378155678-eq5i6 foo=bar
kubectl get pods --show-labels
# NAME                    READY  STATUS   RESTARTS  AGE  LABELS
# ghost-3378155678-eq5i6  1/1    Running  0         11m  foo=bar, pod-template-hash=3378155678,run=ghost
```

For example, if you want to force the scheduling of a pod on a specific node, you can use a nodeSelector in a pod definition, add specific labels to certain nodes in your cluster and use those labels in the pod. See the following example:

```yaml
....
spec:
  containers:
  - image: nginx
  nodeSelector:
    disktype: ssd
```
