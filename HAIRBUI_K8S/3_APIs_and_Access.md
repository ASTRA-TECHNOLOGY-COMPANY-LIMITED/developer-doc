# API Access

## RESTful

> kubectl makes API calls on your behalf, responding to typical HTTP verbs (GET, POST, DELETE). You can also make calls externally, using curl or other program. With the appropriate certificates and keys, you can make requests, or pass JSON files to make configuration changes. See the following command:

```bash
curl --cert userbob.pem --key userBob-key.pem \
--cacert /path/to/ca.pem \
https://k8sServer:6443/api/v1/pods
```

The ability to impersonate other users or groups, subject to RBAC configuration, allows a manual override authentication. This can be helpful for debugging authorization policies of other users.

## Checking Access

While there is more detail on security in a later chapter, it is helpful to check the current authorizations, both as an administrator, as well as another user. The following shows what user bob could do in the default namespace and the developer namespace, using the auth can-i subcommand to query (commands and outputs):

```bash
kubectl auth can-i create deployments
# yes
kubectl auth can-i create deployments --as bob
# no
kubectl auth can-i create deployments --as bob --namespace developer
# yes
```

There are currently three APIs which can be applied to set who and what can be queried:

* **SelfSubjectAccessReview**: Access review for any user, helpful for delegating to others.
* **LocalSubjectAccessReview**: Review is restricted to a specific namespace.
* **SelfSubjectRulesReview**: A review which shows allowed actions for a user within a particular namespace.

> The use of **reconcile** allows a check of authorization necessary to create an object from a file. No output indicates the creation would be allowed.

## Optimistic Concurrency

## Using Annotations

> Labels are used to work with objects or collections of objects; annotations are not.

Having this kind of metadata can be used to track information such as a timestamp, pointers to related objects from other ecosystems, or even an email from the developer responsible for that object's creation.

The annotation data could otherwise be held in an exterior database, but that would limit the flexibility of the data. The more this metadata is included, the easier it is to integrate management and deployment tools or shared client libraries.

For example, to annotate only Pods within a namespace, you can overwrite the annotation, and finally delete it. See the following commands:

```bash
kubectl annotate pods --all description='Production Pods' -n prod
kubectl annotate --overwrite pod webpod description="Old Production Pods" -n prod 
kubectl -n prod annotate pod webpod description-
```

## Simple Pod

Below is an example of a simple pod manifest in YAML format. You can see the apiVersion (it must match the existing API group), the kind (the type of object to create), the metadata (at least a name), and its spec (what to create and parameters), which define the container that actually runs in this pod:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: firstpod
spec:
  containers:
  - image: nginx
    name: stan
```

## Access from Outside the Cluster

The basic server information, with redacted TLS certificate information, can be found in the output of the following command:

```bash
kubectl config view
```

## Namespaces

> The term namespace is used to reference both the kernel feature and the segregation of API objects by Kubernetes. Both are means to keep resources distinct.

4 default namespaces are:

* **default**: The default namespace for new objects.
* **kube-system**: The namespace for system components (infrastructure, monitoring, etc).
* **kube-public**: A namespace readable by all, even those not authenticated.
* **kube-node-lease**: The namespace for worker node leases.

## API Resources with kubectl

> kubectl can list all API resources available in the cluster, as well as the API group, version, and kind.

```bash
kubectl [command] [type] [Name] [flag]
```

Here's the markdown table:

| API Resource | API Resource | API Resource |
|--------------|--------------|--------------|
| all | events (ev) | podsecuritypolicies (psp) |
| certificatesigningrequests (csr) | horizontalpodautoscalers (hpa) | podtemplates |
| clusterrolebindings | ingresses (ing) | replicasets (rs) |
| clusterroles | jobs | replicationcontrollers (rc) |
| clusters (valid only for federation apiservers) | limitranges (limits) | resourcequotas (quota) |
| componentstatuses (cs) | namespaces (ns) | rolebindings |
| configmaps (cm) | networkpolicies (netpol) | roles |
| controllerrevisions | nodes (no) | secrets |
| cronjobs | persistentvolumeclaims (pvc) | serviceaccounts (sa) |
| customresourcedefinition (crd) | persistentvolumes (pv) | services (svc) |
| daemonsets (ds) | poddisruptionbudgets (pdb) | statefulsets (sts) |
| deployments (deploy) | podpreset | storageclasses |
| endpoints (ep) | pods (po) | |

## API Maturity

* **Alpha**: An Alpha level release, noted with *alpha* in the names, may be buggy and is disabled by default. Features could change or disappear at any time, and backward compatibility is not guaranteed. Only use these features on a test cluster which is often rebuilt.
* **Beta**: The Beta levels, found with *beta* in the names, has more well-tested code and is enabled by default. It also ensures that, as changes move forward, they will be tested for backwards compatibility between versions. It has not been adopted and tested enough to be called stable. You can expect some bugs and issues.
* **Stable**: Use of the Stable version, denoted by only an integer which may be preceded by the letter v, is for stable APIs. At the moment, v1 is the only stable version.
