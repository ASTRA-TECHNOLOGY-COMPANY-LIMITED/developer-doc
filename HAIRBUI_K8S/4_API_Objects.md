# API Objects

## v1 API Group

> The v1 API group is the core API group in Kubernetes. It is the default API group and is used for all resources in the cluster.
> The `v1` API group is no longer a single group, but rather a collection of groups for each main object category. For example, there is a `v1` group, a `storage.k8s.io/v1` group, and an `rbac.authorization.k8s.io/v1` group.

### Object

* **Node**: Represents a machine - physical or virtual - that is part of the Kubernetes cluster.
* **Service Account**: Provides an identifier for processes running in a pod to access the API server and performs actions that it is authorized to do.
* **Resource Quota**: Useful tool, allowing you to define quotas per namespace.
* **Endpoint**: Generally, you do not manage endpoints. They represent the set of IPs for pods that match a particular service. They are handy when you want to check that a service actually matches some running pods. If an endpoint is empty, then it means that there are no matching pods and something is most likely wrong with your service definition.

### Deploying an Application

* **Deployment**: It is a controller which manages the state of ReplicaSets and the pods within. The higher level control allows for more flexibility with upgrades and administration. Unless you have a good reason, use a deployment.

* **ReplicaSet**: Orchestrates individual pod lifecycle and updates. These are newer versions of Replication Controllers, which differ only in selector support.

* **Pod**: The lowest unit we can manage; it runs the application container, and possibly support containers.

* **DaemonSet**: Ensures that all (or some) nodes run a copy of a pod. When a new node is added to the cluster, a Pod, same as deployed on the other nodes, is started. When the node is removed, the DaemonSet makes sure the local Pod is deleted. DaemonSets are often used for logging, metrics and security pods, and can be configured to avoid nodes.

* **StatefulSet**: A StatefulSet is the workload API object used to manage stateful applications. Pods deployed using a StatefulSet use the same Pod specification.

> How this is different than a Deployment is that a StatefulSet considers each Pod as unique and provides ordering to Pod deployment
> The differences between stateful and stateless applications is that stateful applications have a unique identity and state, while stateless applications do not. Examples of stateful applications include databases, message queues, and key-value stores.

* **Autoscaling**: In the autoscaling group we find the Horizontal Pod Autoscaler (HPA). HPAs automatically scale the ReplicaSets, Deployments,... based on the CPU (80% default) or memory usage. The usage is checked by the kubelet every 15 seconds, and retrieved by the Metrics Server API call every minute.

> The **Cluster Autoscaler (CA)** adds or removes nodes to the cluster, based on the inability to deploy a Pod or having nodes with low utilization for at least 10 minutes. This allows dynamic requests of resources from the cloud provider and minimizes expenses for unused nodes.

* **Jobs**: Jobs are part of the `batch` API group. They are used to run a set number of pods to completion. If a pod fails, it will be restarted until the number of completion is reached.

> While they can be seen as a way to do batch processing in Kubernetes, they can also be used to run one-off pods. A Job specification will have a parallelism and a completion key. The parallelism key defines the number of pods to run at the same time, while the completion key defines the number of pods to run to completion.

* **Cronjobs**: This is similar to Linux jobs, with the same time syntax. There are some cases where a job would not be run during a time period or could run twice; as a result, the requested Pod should be idempotent.

### RBAC

> The last API resources are in the `rbac.authorization.k8s.io` API group. They are used for role-based access control (RBAC) to Kubernetes resources.

1. **Role**:
    * **Range**: Namespace-scoped (in ONLY 1 namespace)
    * **Function**: Define access permissions to resources within a specific namespace
    * **Example**: Allow reading/writing Pods in namespace "default"

2. **ClusterRole**:
    * **Range**: Cluster-scoped (in cluster)
    * **Function**: Define access permissions to resources across the entire cluster
        * Resources in all namespaces
        * Cluster-scoped resources (nodes, PV...)
        * Non-resource endpoints (/healthz, /metrics...)
    * **Example**: Allow reading/writing Pods in all namespaces

3. **RoleBinding**:
    * **Range**: Namespace-scoped (in ONLY 1 namespace)
    * **Function**: Bind a Role to users/groups/service accounts in 1 namespace

4. **ClusterRoleBinding**:
    * **Range**: Cluster-scoped (in cluster)
    * **Function**: Bind a ClusterRole to users/groups/service accounts in cluster
    * **Important**: Cannot bind Role (namespace-scoped) by ClusterRoleBinding

```yaml
# Role: only read pods in namespace "dev"
kind: Role
namespace: dev
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "list"]

# ClusterRole: read pods in all namespaces
kind: ClusterRole
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "list"]
```

```yaml
# RoleBinding: bind Role to user "dev"
kind: RoleBinding
namespace: dev
subjects:
- kind: User
  name: "dev"
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: "dev"
  apiGroup: rbac.authorization.k8s.io
```

```yaml
# ClusterRoleBinding: bind ClusterRole to user "dev"
kind: ClusterRoleBinding
subjects:
- kind: User
  name: "dev"
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: ClusterRole
  name: "dev"
  apiGroup: rbac.authorization.k8s.io
```
