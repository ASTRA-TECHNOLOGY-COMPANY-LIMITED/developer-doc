# Custom Resource Definitions

## Custom Resources

> To make a new custom resource part of a declarative API, there needs to be a controller to retrieve the structured data continually and act to meet and maintain the declared state. This controller, or operator, is an agent that creates and manages one or more instances of a specific stateful application.
> There are two ways to add custom resources to your Kubernetes cluster. The easiest way, but less flexible, is by adding a **Custom Resource Definition (CRD)** to the cluster. The second way, which is more flexible, is the use of **Aggregated APIs (AA)**, which requires a new API server to be written and added to the cluster.

## Custom Resource Definitions

Custom Resource Definitions are used to define new API objects and controllers.

> If you add a new API object and controller, you can use the existing kube-apiserver to monitor and control the object. The addition of a Custom Resource Definition will be added to the cluster API path, currently under `apiextensions.k8s.io/v1`.

### Example

```yaml
apiVersion: apiextensions.k8s.io/v1
kind: CustomResourceDefinition
metadata:
  name: backups.stable.linux.com
spec:
  group: stable.linux.com
  versions: v1
  scope: Namespaced
  names:
    plural: backups
    singular: backup
    shortNames:
    - bks
    kind: BackUp
```

* **apiVersion**: This should match the current level of stability, which is `apiextensions.k8s.io/v1`
* **name: backups.stable.linux.com**: The name must match the **spec** field declared later. The syntax must be **\<plural name\>.\<group\>**
* **group: stable.linux.com**: The group name will become part of the REST API under **/apis/\<group\>/\<version\>** or **/apis/stable/v1** in this case with the versions set v1
* **scope**: Determines if the object exists in a single namespace or is cluster-wide
* **plural**: Defines the last part of the API URL, such as **/apis/stable/v1/backups**
* **singular and shortNames**: They represent the name displayed and make CLI usage easier
* **kind**: A CamelCased singular type used in resource manifests

### New Object Configuration

```yaml
apiVersion: "stable.linux.com/v1"
kind: BackUp
metadata:
  name: a-backup-object
spec:
  timeSpec: "* * * * */5"
  image: linux-backup-image
replicas: 5
```

> Note that the **apiVersion** and **kind** match the CRD we created in a previous step. The **spec** parameters depend on the controller.
> The object will be evaluated by the controller. If the syntax, such as **timeSpec**, does not match the expected value, you will receive an error, should validation be configured. Without validation, only the existence of the variable is checked, not its details.

### Optional Hooks

Just as with built-in objects, you can use an asynchronous pre-delete hook known as a **Finalizer**. If an API delete request is received, the object metadata field **metadata.deletionTimestamp** is updated. The controller then triggers whichever finalizer has been configured. When the finalizer completes, it is removed from the list. The controller continues to complete and remove finalizers until the string is empty. Then, the object itself is deleted.

```yaml
metadata:
  finalizers:
  - finalizer.stable.linux.com
```

Validation:

```yaml
validation:
    openAPIV3Schema:
      properties:
        spec:
          properties:
            timeSpec:
              type: string
              pattern: '^(\d+|\*)(/\d+)?(\s+(\d+|\*)(/\d+)?){4}$'
            replicas:
              type: integer
              minimum: 1
              maximum: 10
```

> In the example above, the **timeSpec** must be a string matching a particular pattern and the number of allowed replicas is between 1 and 10. If the validation does not match, the error returned is the failed line of validation.

## Understanding Aggregated APIs

> The use of **Aggregated APIs** allows adding additional Kubernetes-type API servers to the cluster. The added server acts as a subordinate to `kube-apiserver`, which runs the aggregation layer in-process. When an extension resource is registered, the aggregation layer watches a passed URL path and proxies any requests to the newly registered API service.

The aggregation layer is easy to enable. Edit the flags passed during startup of the `kube-apiserver` to include `--enable-aggregator-routing=true`.

The creation of the exterior can be done via YAML configuration files or APIs. Configuring TLS authorization between components and RBAC rules for various new objects is also required. A [sample API server](https://github.com/kubernetes/sample-apiserver) is available on GitHub. A project currently in the incubation stage is an [API server builder](https://github.com/kubernetes-sigs/apiserver-builder-alpha) which should handle much of the security and connection configuration.
