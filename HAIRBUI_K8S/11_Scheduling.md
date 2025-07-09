# Scheduling

## kube-scheduler

The scheduler tracks the set of nodes in cluster, filters and scores to determine which node each Pod should be scheduled on. The Pod specification as part of a request is sent to the kubelet on the node for creation.

> The default scheduling decision can be affected through the use of Labels on nodes or Pods. Labels of `podAffinity`, `taints`, and pod bindings allow for configuration from the Pod or the node perspective.
> Some, like tolerations, allow a Pod to work with a node, even when the node has a taint that cannot schedule a Pod.

Some settings will evict Pods from a node should the required condition no longer be true, such as `requiredDuringScheduling`, `requiredDuringExecution`.

## Node Selection in kube-scheduler

* The *Filtering* stage identifies the set of Nodes where the Pod can be scheduled. For example, the `PodFitsResources` filter determines whether a prospective Node has sufficient resources available to satisfy a Pod's particular resource requirements.
* In the *Scoring* stage, the scheduler rates the remaining nodes to determine the best Pod placement. Each Node that made it through filtering is given a score by the scheduler, which is based on the default scheduler configuration.
* The Pod is given to the Node with the highest score by `kube-scheduler`

> **Note**: The filtering and scoring behavior of the scheduler can be configured using scheduling configuration profiles.

## Scheduling Configuration

You can customize the behavior of the `kube-scheduler` by writing a configuration file and passing its path as a command line argument. A scheduling Profile allows you to configure the different stages of scheduling. Each stage is exposed in an extension point.

> An **extension point** is one of the 12 stages of scheduling, at which point a plugin can be used to modify how that state of a scheduler works

## Extension Points

* **queueSort**: These plugins provide an ordering function that is used to sort pending Pods in the scheduling queue. Exactly one queue sort plugin may be enabled at a time.
* **preFilter**: These plugins are used to pre-process or check information about a Pod or the cluster before filtering. They can mark a pod as unschedulable.
* **filter**: These plugins are the equivalent of Predicates in a scheduling Policy and are used to filter out nodes that cannot run the Pod. Filters are called in the configured order. A pod is marked as unschedulable if no nodes pass all the filters.
* **postFilter**: These plugins are the equivalent of Predicates in a scheduling Policy and are used to filter out nodes that cannot run the Pod. Filters are called in the configured order. A pod is marked as unschedulable if no nodes pass all the filters.
* **preScore**: This is an informational extension point that can be used for doing pre-scoring work.
* **score**: These plugins provide a score to each node that has passed the filtering phase. The scheduler will then select the node with the highest weighted scores sum.

More information can be found in the [Kubernetes documentation](https://kubernetes.io/docs/reference/scheduling/config/#profiles).

## Scheduling Plugins

The following plugins, enabled by default, implement one or more of these extension points.

* **ImageLocality**: Favors nodes that already have the container images that the Pod runs. Extension points: **score**.
* **TaintToleration**: Implements taints and tolerations. Implements extension points: **filter**, **preScore**, **score**.
* **NodeName**: Checks if a Pod spec node name matches the current node. Extension points: **filter**.
* **NodePorts**: Checks if a node has free ports for the requested Pod ports. Extension points: **preFilter**, **filter**.
* **NodeAffinity**: Implements node selectors and node affinity. Extension points: **filter**, **score**.
* **PodTopologySpread**: Implements Pod topology spread. Extension points: **preFilter**, **filter**, **preScore**, **score**.
* **NodeUnschedulable**: Filters out nodes that have .spec.unschedulable set to true. Extension points: **filter**.
* **NodeResourcesFit**: Checks if the node has all the resources that the Pod is requesting.
* **NodeResourcesBalancedAllocation**: Favors nodes that would obtain a more balanced resource usage if the Pod is scheduled there. Extension points: **score**.

More information can be found in the [Kubernetes documentation](https://kubernetes.io/docs/reference/scheduling/config/#scheduling-plugins).

## Multiple Profiles

> `kube-scheduler` can be configured to use multiple profiles, each with its own set of plugins and extension points.

```yaml
apiVersion: kubescheduler.config.k8s.io/v1
kind: KubeSchedulerConfiguration
profiles:
  - schedulerName: default-scheduler
  - schedulerName: custom-scheduler
    plugins:
      preFilter:
        disabled:
        - name: '*'
      filter:
        disabled:
        - name: '*'
      postFilter:
        disabled:
        - name: '*'
```

- The scheduler will run with two profiles: one with the default plugins and one with all filtering plugins disabled.
- Pods that want to be scheduled according to a specific profile can include the corresponding scheduler name in its `spec.schedulerName` field.
- By default, one profile with the scheduler name `default-scheduler` is created. This profile includes the default plugins described above. When declaring more than one profile, a unique scheduler name for each profile must be specified.

> **Note**: If a scheduler name is not specified in the pod spec, `kube-apiserver` will set it to `default-scheduler`. Therefore, a profile with this scheduler name should exist to get those pods scheduled.

## Pod Specification

Most scheduling decisions can be made as part of the Podspec. A pod specification contains several fields that inform scheduling:

* **nodeName** and **nodeSelector**: Allow a Pod to be assigned to a single node or a group of nodes with particular labels
* **affinity** and **anti-affinity**: Those can be used to require or prefer which node is used by the scheduler. If using a preference instead, a matching node is chosen first, but other nodes would be used if no match is present
* **taints** and **tolerations**: The use of taints allows a node to be labeled such that Pods would not be scheduled for some reason, such as the cp node after initialization. A toleration allows a Pod to ignore the taint and be scheduled assuming other requirements are met
* **schedulerName**: If none of the options above meet the needs of the cluster, there is also the ability to deploy a custom scheduler. Each Pod could then include a `schedulerName` to choose which schedule to use

## Scheduler Profiles

Another way to configure the scheduler is to use profiles. Profiles allow you to configure the different stages of scheduling. Each stage is exposed in an extension point.

An extension point is one of the 12 stages of scheduling, at which point a plugin can be used to modify how that state of a scheduler works.

* queueSort
* preFilter
* filter
* postFilter
* preScore
* score
* reserve
* permit
* preBind
* bind
* postBind
* multiPoint

There are quite a few plugins which are enabled, or can be enabled, to effect how the scheduler chooses a node for a podSpec. You can take a look at the current [scheduling plugins](https://kubernetes.io/docs/reference/scheduling/config/#scheduling-plugins) options.

## Pod Affinity Rules

Pods which may communicate a lot or share data may operate best if co-located, which would be a form of affinity. For greater fault tolerance, you may want Pods to be as separate as possible, which would be anti-affinity. These settings are used by the scheduler based on the labels of Pods that are already running. As a result, the scheduler must interrogate each node and track the labels of running Pods

> Pod affinity rules use **In**, **NotIn**, **Exists** and **DoesNotExist** operator

* **requiredDuringSchedulingIgnoredDuringExecution**: The Pod will not be scheduled on a node unless the following operator is true. If the operator changes to become false in the future, the Pod will continue to run. This could be seen as a hard rule.
* **preferredDuringSchedulingIgnoredDuringExecution**: Choose a node with the desired setting before those without. If no properly-labeled nodes are available, the Pod will execute anyway. This is more of a soft setting, which declares a preference instead of a requirement.
* **podAffinity**: With the use of podAffinity, the scheduler will try to schedule Pods together.
* **podAntiAffinity**: The use of podAntiAffinity would cause the scheduler to keep Pods on different nodes.

## podAffinity Example

An example of **affinity** and **podAffinity** settings can be seen below. This also requires a particular label to be matched when the Pod starts, but not required if the label is later removed.

```yaml
spec:
  affinity:
    podAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
      - labelSelector:
          matchExpressions:
          - key: security
            operator: In
            values:
            - S1
```

> The Pod can be scheduled on a node running a Pod with a key label of security and a value of S1. If this requirement is not met, the Pod will remain in a Pending state.

## podAntiAffinity Example

With **podAntiAffinity**, we can prefer to avoid nodes with a particular label. In this case, the scheduler will prefer to avoid a node running a pod that has a key label of security and value of S2.

```yaml
podAntiAffinity:
  preferredDuringSchedulingIgnoredDuringExecution:
  - weight: 100
    podAffinityTerm:
      labelSelector:
        matchExpressions:
        - key: security
          operator: In
          values:
          - S2
```

> As a preference, this setting tries to avoid certain labels, but will still schedule the Pod on some node. As the Pod will still run, we can provide a weight to a particular rule. The weights can be declared as a value from 1 to 100. The scheduler then tries to choose, or avoid the node with the greatest combined value.

## Taints

A node with a particular taint will repel Pods without tolerations for that taint. A taint is expressed as `key=value:effect`.

### Ways to Handle Pod Scheduling

* **NoSchedule**: The scheduler will not schedule a Pod on this node, unless the Pod has this toleration. Existing Pods continue to run, regardless of toleration.
* **PreferNoSchedule**: The scheduler will avoid using this node, unless there are no untainted nodes for the Pods toleration. Existing Pods are unaffected.
* **NoExecute**: This taint will cause existing Pods to be evacuated and no future Pods scheduled. Should an existing Pod have a toleration, it will continue to run. If the Pod tolerationSeconds is set, they will remain for that many seconds, then be evicted. Certain node issues will cause the kubelet to add 300 second tolerations to avoid unnecessary evictions.

## Tolerations

> Setting tolerations on a node are used to schedule Pods on tainted nodes. This provides an easy way to avoid Pods using the node. Only those with a particular toleration would be scheduled.

An operator can be included in a Pod specification, defaulting to **Equal** if not declared. The use of the operator **Equal** requires a value to match. The **Exists** operator should not be specified. If an empty key uses the **Exists** operator, it will tolerate every taint.

```yaml
tolerations:
- key: "server"
  operator: "Equal"
  value: "ap-east"
  effect: "NoExecute"
  tolerationSeconds: 3600
```

The Pod will remain on the server with a key of server and a value of ap-east for 3600 seconds after the node has been tainted with NoExecute. When the time runs out, the Pod will be evicted.

## Custom Scheduler

If the default scheduling mechanisms (affinity, taints, policies) are not flexible enough for your needs, you can write your own scheduler. The programming of a custom scheduler is outside the scope of this course, but you may want to start with the existing scheduler code, which can be found in the [Scheduler repository on GitHub](https://github.com/kubernetes/kubernetes/tree/master/pkg/scheduler).

The end result of the scheduling process is that a pod gets a binding that specifies which node it should run on. A binding is a Kubernetes API primitive in the api/v1 group. Technically, without any scheduler running, you could still schedule a pod on a node, by specifying a binding for that pod.

You can also run multiple schedulers simultaneously.

You can view the scheduler and other information with this command:

```bash
kubectl get events
```
