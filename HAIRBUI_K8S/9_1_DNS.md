# DNS

> DNS has been provided as CoreDNS by default as of v1.13. Once the container starts, it will run a Server for the zones it has been configured to serve. Then, each server can load one or more plugin chains to provide other functionality. As with other microservices, clients would access it using a service, `kube-dns`.

DNS plays an important role in Kubernetes, as it is used to resolve the IP addresses of services and pods. It is also used to resolve the IP addresses of external services, such as those provided by cloud providers.

## FQDN (Full Qualified Domain Name)

### Structure FQDN for a Service

**FQDN** of a Service in Kubernetes follows the pattern below:

`<service-name>.<namespace>.svc.<cluster-domain>`

* `<service-name>`: The name of the service.
* `<namespace>`: The namespace of the service.
* `<cluster-domain>`: The cluster domain, which is typically `cluster.local`.

For example, a Service named `my-service` in namespace `default` will have FQDN:

`my-service.default.svc.cluster.local`

### Structure FQDN for a Pod

**FQDN** of a Pod in Kubernetes follows the pattern below:

`<pod-name>.<namespace>.pod.<cluster-domain>`

* `<pod-name>`: The name of the pod.
* `<namespace>`: The namespace of the pod.
* `<cluster-domain>`: The cluster domain, which is typically `cluster.local`.

For example, a Pod named `my-pod` in namespace `default` will have FQDN:

`my-pod.default.pod.cluster.local`

## Service DNS Records

### A/AAAA Records

Kubernetes create **A records** (IPv4) or **AAAA records** (IPv6) for each Service:

* Normal Service: A record pointed to the ClusterIP of the Service
* Headless Service: A record pointed to the Ephemeral IP of the Pod (A temporary IP address assigned to a pod when it is created)

### SRV Records

**SRV records** are created for named ports of a Service with format:

`_<port-name>._<protocol>.<service-name>.<namespace>.svc.<cluster-domain>`

For example, if you have a Service configured like:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-webapp
  namespace: production
spec:
  selector:
    app: webapp
  ports:
  - name: http
    protocol: TCP
    port: 80
    targetPort: 8080
  - name: https
    protocol: TCP
    port: 443
    targetPort: 8443
```

Kubernetes will auto create SRV records:

* `_http._tcp.my-webapp.production.svc.cluster.local`
* `_https._tcp.my-webapp.production.svc.cluster.local`

And you can exec to a Pod and use `nslookup` to query the SRV records:

```bash
kubectl exec -it my-pod -- nslookup _http._tcp.my-webapp.production.svc.cluster.local

# Server:		10.96.0.10
# Address:	10.96.0.10#53

# _http._tcp.my-webapp.production.svc.cluster.local	service = 0 100 80 my-webapp.production.svc.cluster.local.
```

### Headless Service

A **headless service** is a service that does not have a ClusterIP (`clusterIP: None`). It is different from a normal service in that it does not have a ClusterIP and does not load balance traffic to the pods.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-headless-service
spec:
  clusterIP: None  # This will create a headless service
  selector:
    app: my-app
  ports:
  - port: 80
    targetPort: 8080
```

#### DNS Resolution for Headless Service

When querying DNS for a headless service, **CoreDNS will return the A record for all Pod matching the selector with format**:

```bash
# Assume that you have 3 Pods behind a service
# Pod 1: 10.244.1.5
# Pod 2: 10.244.1.6
# Pod 3: 10.244.1.7

# Normal Service
nslookup my-service.default.svc.cluster.local

# 10.96.0.10 (This is the ClusterIP of the Service, NOT the Pod IP)
# Every traffic sent to this IP will be load balanced to the pods

# Headless Service
nslookup my-headless-service.default.svc.cluster.local

# 172.17.0.3 (Pod 1 IP)
# 172.17.0.4 (Pod 2 IP)
# 172.17.0.5 (Pod 3 IP)

# Client can connect to any of the pods directly
```

#### Use Cases for Headless Service

* Accessing a Pod directly
* **StatefulSet**: Headless service is often used with StatefulSet to provide a stable network identity for each pod.
* **Database Clusters**: Client can connect to a specific pod in the cluster using the headless service.
* **Service Discovery**: Application self-managed load balancing.

#### Example Use Case

Assume that you have a PostgreSQL cluster with 1 master and 2 read slave replicas. Your application will need to do the following:

* Send all WRITE queries to the master
* Distribute READ queries to all 3 nodes (master + 2 slave replicas) using load balancing

- With normal service (non-headless), you cannot do this efficiently:

```python
# With normal service - CANNOT control
def execute_query(query, is_write=False):
  # Connect to service
  conn = psycopg2.connect("host=postgres-service port=5432")

  # Issue: Cannot control which pod the query will be sent to!
  # WRITE can be sent to random slave slave replica = ERROR!
  cursor = conn.cursor()
  cursor.execute(query)
```

- With headless service, you have full control:

```python
# With headless service - CAN control
import dns.resolver
import random

def get_postgres_endpoints():
  # Query headless service, receive ALL pod IPs
  answers = dns.resolver.resolve('postgres-headless', 'A')
  return [str(rdata) for rdata in answers]

def execute_query(query, is_write=False):
  endpoints = get_postgres_endpoints()

  if is_write:
    # WRITE queries MUST go to the first pod (master)
    # StatefulSet ensures postgres-0 is always the master
    host = "postgres-0.postgres-headless"
  else:
    # READ queries distributed to ALL pods
    host = random.choice(endpoints)

  conn = psycopg2.connect(f"host={host} port=5432")
  cursor = conn.cursor()
  cursor.execute(query)
```

### Pod DNS

#### Pod Hostname and Subdomain

Pods can be configured with **hostname** and **subdomain** to create a customized FQDN:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  hostname: custom-hostname      # Hostname of the Pod
  subdomain: my-subdomain        # Subdomain of the Pod
  containers:
  - name: nginx
    image: nginx
```

Therefore, FQDN of this Pod will be:

`custom-hostname.my-subdomain.default.pod.cluster.local`

#### StatefulSet and Stable Network Identity

**StatefuleSet** auto setting **hostname** and **subdomain** for each Pod, create stable network identities:

```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: web
spec:
  serviceName: "nginx"  # Headless Service name
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx
```

Pods will have FQDNs like:

`web-0.nginx.default.svc.cluster.local`
`web-1.nginx.default.svc.cluster.local`
`web-2.nginx.default.svc.cluster.local`

#### IP based DNS

It's possible to resolve a FQDN like `IP.NAMESPACE.pod.cluster.local` to get the IP address of a pod.

> **IMPORTANT**: IP will be displayed with dash instead of dot, for example `10-244-1-5` instead of `10.244.1.5`

```bash
nslookup 10-244-1-5.default.pod.cluster.local

# Server: 10.96.0.10
# Address: 10.96.0.10#53

# 10-244-1-5.default.pod.cluster.local
# Address: 10.244.1.5
```
