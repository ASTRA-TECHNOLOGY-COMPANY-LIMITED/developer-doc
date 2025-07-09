# Ingress

## Ingress Controller

> An Ingress Controller is a daemon running in a Pod which watches the `/ingresses` API endpoint on the `kube-apiserver`, which is found under the `networking.k8s.io/v1` group for new objects. When a new endpoint is created, the daemon uses the configured set of rules to allow inbound connection to a service, most often HTTP traffic.
> This allows easy access to a service through an edge router to Pods, regardless of where the Pod is deployed.

Multiple Ingress Controllers can be deployed, and they will each watch the `/ingresses` API endpoint and apply their own rules to allow inbound connection to a service. Traffic uses `ingressClassName` to determine which Ingress Controller to use.

![ingress_inbound_connections](https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/5ln4zg2183da-TheIngress.png)

## Nginx

Deploying an nginx controller has been made easy through the use of provided YAML files, which can be found in the [ingress-nginx/docs/deploy GitHub repository](https://github.com/kubernetes/ingress-nginx/blob/main/docs/deploy/index.md).

* Easy integration with RBAC
* Uses the `ingressClassName` field to determine which Ingress Controller
* L7 traffic requires the `proxy-real-ip-cidr` setting
* Bypasses `kube-proxy` to allow session affinity
* Does not use conntrack entries for iptables DNAT
* TLS requires the `host` field to be defined

## Creating an Ingress Rule

First, start a ghost deployment and expose it with an internal ClusterIP service. See the following commands:

```bash
kubectl run ghost --image=ghost
kubectl expose deployments ghost --port=2368
```

Then, create ingress rule:

```yaml
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
rules:
  - host: ghost.192.168.99.100.nip.io
    http:
      paths:
      - backend:
          service
            name: ghost
            port:
              number: 2368
        path: /
        pathType: ImplementationSpecific
```

> Note: Replace `ghost.192.168.99.100.nip.io` with your own domain.

## Intelligent Connected Proxies

> For more complex connections or resources such as service discovery, rate limiting, traffic management and advanced metrics, you may want to implement a service mesh.

A **service mesh** consists of edge and embedded proxies communicating with each other and handling traffic based on rules from a control plane. Various options are available, including **Envoy**, **Istio**, and **linkerd**.

![istio_architecture_sidecar](https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/7yq65t54u0bu-Istioarchitectureinsidecarmode.png)

## Ingress Limitations

### Support

The Ingress spec only supports HTTP(S) routing on host/path and TLS termination - no native support for TCP, gRPC, header-based routing, canary traffic splitting, etc.

To implement anything beyond the basics, you must use annotations (Like in nginx ingress controller), which are non-standard and vary between controllers.

### Role

While you can restric access to Ingress via RBAC, there is still no built-in way for manage TLS and manage routing in the same ingress, you will need full access to the whole Ingress spec.

## Gateway API

Gateway API has three stable API kinds and belongs to `gateway.networking.k8s.io/v1` group

* **GatewayClass**: Defines a set of gateways with a common configuration and is managed by a controller that implements the class.
* **Gateway**: Defines an instance of traffic handling infrastructure such as a cloud load balancer
* **HTTPRoute**: Defines a set of routes for mapping HTTP traffic from a Gateway listener to a representation of backend network endpoints (often a Kubernetes service)

### Advantages

* Include standard support for L4/L7 protocols, advanced routing (headers, queries), traffic splitting, mirroring, and more.
* Gateway API is highly extensible through well-defined mechanisms (custom route filters, policies, new route types) without breaking the API's consistency.

## GatewayClass

> A GatewayClass is a cluster-wide resource that defines a set of gateways with a common configuration and is managed by a controller that implements the class.
> The most critical aspect of a GatewayClass is the `controllerName` field, which is used to identify the controller that implements the class.

```yaml
apiVersion: gateway.networking.k8s.io/v1
kind: GatewayClass
metadata:
  name: eg
spec:
  controllerName: gateway.envoyproxy.io/gatewayclass-controller
```

> Note: Replace `gateway.envoyproxy.io/gatewayclass-controller` with your own controller name.

## Gateway

> A Gateway in Kubernetes serves as the designated entry point for external traffic, orchestrating how requests are received and processed by defining listener configurations for various protocols and ports. It works in parallel with GatewayClass, which specifies the controller that implements the actual networking infrastructure.

```yaml
apiVersion: gateway.networking.k8s.io/v1
kind: Gateway
metadata:
  name: example-gateway
spec:
  gatewayClassName: eg
  listeners:
  - name: http
    protocol: HTTP
    port: 80
```

> Note: Replace `eg` with your own gateway class name.

## HTTPRoute

> HTTPRoute is a dedicated resource that specifies how HTTP traffic should be routed to backend network endpoints (often a Kubernetes service).

```yaml
apiVersion: gateway.networking.k8s.io/v1
kind: HTTPRoute
metadata:
  name: example-httproute
spec:
  parentRefs:
  - name: example-gateway
  hostnames:
  - "www.example.com"
  rules:
  - matches:
    - path:
      type: PathPrefix
      value: /login
    backendRefs:
    - name: example-svc
      port: 8080
```
