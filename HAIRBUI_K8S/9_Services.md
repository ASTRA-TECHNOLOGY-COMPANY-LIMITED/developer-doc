# Services

> Services are the agents which connect Pods together, or provide access outside of the cluster. Typically using Labels, the refreshed Pod is connected and the microservice continues to provide the expected resource via an Endpoint object.

## Service update pattern

> Labels are used to determine which Pods should receive traffic from a service. Labels can be dynamically updated for an object, which may affect which Pods continue to connect to a service.
> The default update pattern is for a rolling deployment, where new Pods are added, with different versions of an application, and due to automatic load balancing, receive traffic along with previous versions of the application.

## Accessing an Application with a Service

```bash
kubectl expose deployment/nginx --port=80 --type=NodePort
kubectl get svc
# NAME        TYPE       CLUSTER-IP  EXTERNAL-IP  PORT(S)        AGE
# kubernetes  ClusterIP  10.0.0.1    <none>       443/TCP        18h
# nginx       NodePort   10.0.0.112  <none>       80:31230/TCP   5s
kubectl get svc nginx -o yaml
# apiVersion: v1
# kind: Service
# ...
# spec:
#     clusterIP: 10.0.0.112
#     ports:
#     - nodePort: 31230
```

## Service Types

* **ClusterIP**: Expose the service on a cluster-internal IP. This is the default.
* **NodePort**: The NodePort type is great for debugging, or when a static IP address is necessary, such as opening a particular address through a firewall. The port is typically in the range of 30000-32767.
* **LoadBalancer**: The LoadBalancer type is great for when you want to expose a service to the internet. The cloud provider (e.g. AWS, GCP, Azure) will create a load balancer and assign an external IP address to the service.
* **ExternalName**: This service type has no selectors, nor does it define ports or endpoints. It allows the return of an alias to an external service. The redirection happens at the DNS level, not via a proxy or forward.

> A Service is an operator running inside the `kube-controller-manager`, which sends API calls via the `kube-apiserver` to the Network Plugin (such as **Cilium**) and the `kube-proxy` pods running all nodes. The Service operator also creates an **Endpoint** operator, which queries for the ephemeral IP addresses of pods with a particular label. These agents work together to manage firewall rules using `iptables` or `ipvs`.

![built_in_service](https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/bicxpld1h6ar-Service_Relationships.png)

1. The **ClusterIP** service configures a persistent IP address and directs traffic sent to that address to the existing pod's ephemeral addresses. This only handles inside the cluster traffic.
2. When a request for a **NodePort** is made, the operator first creates a **ClusterIP**. After the **ClusterIP** has been created, a high numbered port is determined and a firewall rule is sent out so that traffic to the high numbered port on any node will be sent to the persistent IP, which then will be sent to the pod(s).
3. A **LoadBalancer** does not create a load balancer. Instead, it creates a **NodePort** and makes an async request to use a load balancer. If a listener sees the request, as found when using public cloud providers, one would be created. Otherwise, the status will remain Pending as no load balancer has responded to the API call.

> An **ingress controller** is a microservice running in a pod, listening to a high port on whichever node the pod may be running, which will send traffic to a Service based on the URL requested. It is not a built-in service, but is often used with services to centralize traffic to services. More on an ingress controller is found in a future chapter.

## Services Diagram

> The controllers of **services** and **endpoints** run inside the `kube-controller-manager` and send API calls to the `kube-apiserver`. API calls are then sent to the network plugin, such as `cilium-controller`, which then communicates with agents on each node, such as `cilium-node`. Every `kube-proxy` is also sent an API call so that it can manage the firewall locally. The firewall is often `iptables` or `ipvs`.

![services_diagram](https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/4i914tw38hd9-ServicesDiagramProxy-2023.png)

In the `iptables` proxy mode, `kube-proxy` continues to get updates from the API server for changes in **Service** and **Endpoint** objects, and updates rules for each object when created or removed.

The graphic above shows two workers, each with a replica of **MyApp** running. A NodePort has been configured, which will direct traffic from port 35001 to the **ClusterIP** and on to the ephemeral IP of the pod. All nodes use the same firewall rule. As a result, you can connect to any node, and Cilium will get the traffic to a node which is running the pod.

## Overall Network view

![cluster_networking](https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/k9384xxsvbjw-Service_Network.png)

An example of a multi-container pod with two services sending traffic to its ephemeral IP can be seen in the diagram. The diagram also shows an **ingress controller**, which would typically be represented as a pod, but has a different shape to show that it is listening to a high numbered port of an interface and is sending traffic to a service. Typically, the service the **ingress controller** sends traffic to would be a **ClusterIP**, but the diagram shows that it would be possible to send traffic to a **NodePort** or a **LoadBalancer**.

## Local Proxy for Development

> When developing an application or service, one quick way to check your service is to run a local proxy with `kubectl`. When running, you can make calls to the Kubernetes API on localhost and also reach the ClusterIP services on their API URL. The IP and port where the proxy listens can be configured with command arguments.

```bash
kubectl proxy
# Starting to serve on 127.0.0.1:8001
```

### Kubernetes Docker Desktop

> In case you are using Kubernetes Docker Desktop, you can use `kube-proxy` image to run a local proxy.

```bash
docker run -d --name kube-proxy \
  --restart always \
  -v $HOME/.kube/config:/root/.kube/config:ro \
  -p 8001:8001 \
  alpine/k8s:1.30.13 \
  kubectl proxy --address='0.0.0.0' --port=8001 --accept-hosts='^.*$'
```

> Note: Replace `1.30.13` with your Kubernetes version. Go to [alpine/k8s image tags](https://hub.docker.com/r/alpine/k8s/tags) to check for available tags.

### Zone

A zone in DNS is like a namespace or domain that the DNS server is responsible for. Think of it as a specific area of the DNS hierarchy that a server manages. In Kubernetes context:

* The main zone is typically `cluster.local`, which is the default for CoreDNS.
* This means CoreDNS is authoritative for all DNS names ending in `.cluster.local`.
* For example, if you have a service named `myapp` in the `default` namespace, CoreDNS will resolve `myapp.default.svc.cluster.local` to the IP address of the service.

> To add a new zone, change configuration of CoreDNS by using `kubectl edit configmap coredns -n kube-system`.

### Server

> A **server** in CoreDNS is a DNS server instance that listens on specific ports and handles DNS queries for configured zones. When CoreDNS starts:

1. It creates a server instance
2. The server is configured to handle specific zones (like `cluster.local`)
3. The server loads plugins that define how to process DNS queries

### Plugin Chain

Each server can load multiple plugins that work together in a chain. For example:

* kubernetes plugin: Handles DNS for Kubernetes services and pods
* forward plugin: Forwards external DNS queries to upstream DNS servers
* cache plugin: Caches DNS responses for better performance

### The `kube-dns` Service

> Even though the actual DNS server is CoreDNS, it's accessed through a Kubernetes service called `kube-dns`. This provides:

* A stable IP address for DNS queries
* Load balancing if multiple CoreDNS instances are running
* The standard way for pods to reach the DNS server
