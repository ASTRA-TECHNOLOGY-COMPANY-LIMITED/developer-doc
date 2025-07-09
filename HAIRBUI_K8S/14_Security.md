# Security

## Accessing the API

To perform any action in a Kubernetes cluster, you need to access the API and go through three main steps:

* Authentication (token)
* Authorization (RBAC)
* Admission Controllers

These steps are described in more detail in the official documentation about [controlling access to the Kubernetes API](https://kubernetes.io/docs/concepts/security/controlling-access/) and illustrated by the diagram below.

![accessing_api](https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/1u7mjwewyqon-Accessing_the_API.png)

1. Once a request reaches the API server securely, it will first go through any authentication module that has been configured. The request can be rejected if authentication fails or it gets authenticated and passed to the authorization step.

2. At the authorization step, the request will be checked against existing policies. It will be authorized if the user has the permissions to perform the requested actions. Then, the requests will go through the last step of admission. In general, admission controllers will check the actual content of the objects being created and validate them before admitting the request.

3. In addition to these steps, the requests reaching the API server over the network are encrypted using TLS. This needs to be properly configured using SSL certificates.

## Authentication

There are three main points to remember with authentication in Kubernetes:

* In its straightforward form, authentication is done with certificates, tokens or basic authentication (i.e. username and password).
* Users are not created by the API, but should be managed by an external system.
* System accounts are used by processes to access the API (to learn more read [Configure Service Accounts for Pods](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/)).

The type of authentication used is defined in the kube-apiserver startup options. Below are four examples of a subset of configuration options that would need to be set depending on what choice of authentication mechanism you choose:

* **--basic-auth-file**
* **--oidc-issuer-url**
* **--token-auth-file**
* **--authorization-webhook-config-file**

## Authorization

There are two main authorization modes and two global Deny/Allow settings. The main modes are:

* **RBAC**
  * [Role Based Access Control](https://kubernetes.io/docs/reference/access-authn-authz/rbac/)
  * Rules are operations which can act upon an API group. Roles are a group of rules which affect, or scope, a single namespace, whereas ClusterRoles have a scope of the entire cluster.
  * RBAC is then writing rules to allow or deny operations by users, roles or groups upon resources.
  * Here is a summary of the RBAC process:
    * Determine or create namespace
    * Create certificate credentials for user
    * Set the credentials for the user to the namespace using a context
    * Create a role for the expected task set
    * Bind the user to the role
    * Verify the user has limited access.
* **Webhook**
  * A Webhook is an HTTP callback, an HTTP POST that occurs when something happens; a simple event-notification via HTTP POST. A web application implementing Webhooks will POST a message to a URL when certain things happen.

They can be configured as kube-apiserver startup options:

* **--authorization-mode=RBAC**
* **--authorization-mode=Webhook**
* **--authorization-mode=AlwaysDeny**
* **--authorization-mode=AlwaysAllow**

## Admission Controllers

The last step in letting an API request into Kubernetes is admission control.

> Admission controllers are pieces of software that can access the content of the objects being created by the requests. They can modify the content or validate it, and potentially deny the request.

Admission controllers are needed for certain features to work properly. Controllers have been added as Kubernetes matured. The admission controllers are now compiled into the binary, instead of a list passed during execution. To enable or disable, you can pass the following options, changing out the plugins you want to enable or disable:

```bash
--enable-admission-plugins=Initializers,NamespaceLifecycle,LimitRanger
--disable-admission-plugins=PodNodeSelector
```

The first controller is **Initializers** which will allow the dynamic modification of the API request, providing great flexibility. Each admission controller functionality is explained in the documentation. For example, the **ResourceQuota** controller will ensure that the object created does not violate any of the existing quotas.

## Security Contexts

Pods and containers within pods can be given specific security constraints to limit what processes running in containers can do. For example, the UID of the process, the Linux capabilities, and the filesystem group can be limited.

> This security limitation is called a **security context**. It can be defined for the entire pod or per container, and is represented as additional sections in the resources manifests. The notable difference is that Linux capabilities are set at the container level.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  securityContext:
    runAsNonRoot: true
  containers:
  - image: nginx
    name: nginx
```

Then, when you create this Pod, you will see a warning that the container is trying to run as root and that it is not allowed. Hence, the Pod will never run. See the following command and its output:

```bash
kubectl get pods
# NAME   READY  STATUS                                                 RESTARTS  AGE
# nginx  0/1    container has runAsNonRoot and image will run as root  0         10s
```

## Network Security Policies

> By default, all pods can reach each other; all ingress and egress traffic is allowed. his has been a high-level networking requirement in Kubernetes. However, network isolation can be configured and traffic to pods can be blocked. In newer versions of Kubernetes, egress traffic can also be blocked. This is done by configuring a **NetworkPolicy**.

The **spec** of the policy can narrow down the effect to a particular namespace, which can be handy. Further settings include a **podSelector**, or label, to narrow down which Pods are affected. Further ingress and egress settings declare traffic to and from IP addresses and ports.

Not all network providers support the **NetworkPolicies** kind. A non-exhaustive list of providers with support includes Calico, Romana, Cilium, Kube-router, and WeaveNet.

### Example

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: ingress-egress-policy
  namespace: default
spec:
  podSelector:
    matchLabels:
      role: db
  policyTypes:
    - Ingress
    - Egress
  ingress:
    - from:
      - ipBlock:
        cidr: 172.17.0.0/16
        except:
          - 172.17.1.0/24
      - namespaceSelector:
        matchLabels:
          project: myproject
      - podSelector:
        matchLabels:
          role: frontend
      ports:
        - protocol: TCP
          port: 6379
  egress:
    - to:
      - ipBlock:
        cidr: 10.0.0.0/24
      ports:
        - protocol: TCP
          port: 5978
```

* Only Pods with the label of **role: db** will be affected by this policy, and the policy has both Ingress and Egress settings.
* The ingress setting includes a **172.17** network, with a smaller range of **172.17.1.0** IPs being excluded from this traffic.
* These rules change the namespace for the following settings to be labeled **project: myproject**. The affected Pods also would need to match the label **role: frontend**. Finally, TCP traffic on port 6379 would be allowed from these Pods.
* The egress rules have the **to** settings, in this case the **10.0.0.0/24** range TCP traffic to port 5978.

> **NOTE**: The use of empty ingress or egress rules denies all type of traffic for the included Pods, though this is not suggested. Use another dedicated **NetworkPolicy** instead.

## Default Policy Example

The empty braces will match all Pods not selected by other **NetworkPolicy** and will not allow ingress traffic. Egress traffic would be unaffected by this policy.

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: default-deny
spec:
  podSelector: {}
  policyTypes:
  - Ingress
```

Some network plugins, such as WeaveNet, may require annotation of the Namespace. The following shows the setting of a **DefaultDeny** for the **myns** namespace:

```yaml
kind: Namespace
apiVersion: v1
metadata:
  name: myns
  annotations:
    net.beta.kubernetes.io/network-policy: |
     {
      "ingress": {
        "isolation": "DefaultDeny"
      }
     }
```
