# Gateway API

> If you want to use Gateway API, you need to install something called gateway controller that implements the Gateway API.
> In my case, I will suggest using Nginx Gateway Fabric

## Add certificates for secure connection (optional)

> NGINX Gateway Fabric consists of:

* Control Plane (NGINX Gateway Fabric controller) - Manages configuration
* Data Plane (NGINX pods with NGINX Agent) - Handles actual traffic

Therefore, they need to secure communication between them by using mTLS.

NGINX Pod (Data Plane)          NGINX Gateway Fabric (Control Plane)
┌─────────────────────┐        ┌──────────────────────────────────┐
│                     │        │                                  │
│     NGINX Agent     │  TLS   │      Control Plane Service       │
│   (uses agent-tls   │------->│        (uses server-tls          │
│    as client cert)  │        │         as server cert)          │
│                     │        │                                  │
└─────────────────────┘        └──────────────────────────────────┘

```bash
# Add jetstack repo
helm repo add jetstack https://charts.jetstack.io
helm repo update

# Install cert-manager
helm install \
  cert-manager jetstack/cert-manager \
  --namespace cert-manager \
  --create-namespace \
  --set config.apiVersion="controller.config.cert-manager.io/v1alpha1" \
  --set config.kind="ControllerConfiguration" \
  --set config.enableGatewayAPI=true \
  --set crds.enabled=true

# Create namespace for nginx gateway
kubectl create namespace nginx-gateway

# Create issuer
kubectl apply -f - <<EOF
apiVersion: cert-manager.io/v1
kind: Issuer
metadata:
  name: selfsigned-issuer
  namespace: nginx-gateway
spec:
  selfSigned: {}
---
apiVersion: cert-manager.io/v1
kind: Certificate
metadata:
  name: nginx-gateway-ca
  namespace: nginx-gateway
spec:
  isCA: true
  commonName: nginx-gateway
  secretName: nginx-gateway-ca
  privateKey:
    algorithm: RSA
    size: 2048
  issuerRef:
    name: selfsigned-issuer
    kind: Issuer
    group: cert-manager.io
---
apiVersion: cert-manager.io/v1
kind: Issuer
metadata:
  name: nginx-gateway-issuer
  namespace: nginx-gateway
spec:
  ca:
    secretName: nginx-gateway-ca
EOF

# Create certificate
# Notice those certificates below just are used to secure communication between agent and control plane
kubectl apply -f - <<EOF
apiVersion: cert-manager.io/v1
kind: Certificate
metadata:
  name: nginx-gateway
  namespace: nginx-gateway
spec:
  secretName: server-tls
  usages:
  - digital signature
  - key encipherment
  dnsNames:
  - ngf-nginx-gateway-fabric.nginx-gateway.svc # this value may need to be updated
  issuerRef:
    name: nginx-gateway-issuer
EOF

kubectl apply -f - <<EOF
apiVersion: cert-manager.io/v1
kind: Certificate
metadata:
  name: nginx
  namespace: nginx-gateway
spec:
  secretName: agent-tls
  usages:
  - "digital signature"
  - "key encipherment"
  dnsNames:
  - "*.cluster.local"
  issuerRef:
    name: nginx-gateway-issuer
EOF
```

## Install Nginx Gateway Fabric

In my case, I'm using [helm](https://docs.nginx.com/nginx-gateway-fabric/install/helm/) to install NGF

```bash
# Install CRDs
kubectl kustomize "https://github.com/nginx/nginx-gateway-fabric/config/crd/gateway-api/standard?ref=v2.0.2" | kubectl apply -f -

# Install NGF
helm install ngf oci://ghcr.io/nginx/charts/nginx-gateway-fabric --create-namespace -n nginx-gateway
```

## Deploy a Gateway for data plane instances

> A Gateway is used to manage all inbound requests, and is a key Gateway API resource.
> When a Gateway is attached to a GatewayClass associated with NGINX Gateway Fabric, it creates a Service and an NGINX deployment. This forms the NGINX data plane, handling requests.
> A single GatewayClass can have **multiple Gateways**: each Gateway will create a separate Service and NGINX deployment.

```bash
# Create custom gateway based on a single GatewayClass
kubectl apply -f - <<EOF
apiVersion: gateway.networking.k8s.io/v1
kind: Gateway
metadata:
  name: cafe
spec:
  gatewayClassName: nginx
  listeners:
  - name: http
    port: 80
    protocol: HTTP
EOF
```

## Routing traffic to applications

```bash
# Deploy coffee app with service
kubectl apply -f - <<EOF
apiVersion: apps/v1
kind: Deployment
metadata:
  name: coffee
spec:
  replicas: 2
  selector:
    matchLabels:
      app: coffee
  template:
    metadata:
      labels:
        app: coffee
    spec:
      containers:
      - name: coffee
        image: nginxdemos/nginx-hello:plain-text
        ports:
        - containerPort: 8080
---
apiVersion: v1
kind: Service
metadata:
  name: coffee
spec:
  ports:
  - port: 80
    targetPort: 8080
    protocol: TCP
    name: http
  selector:
    app: coffee
EOF
```

> To route traffic to the **coffee** application, we will create a Gateway and HTTPRoute. The following diagram shows the configuration we are creating in the next step:

![routing_traffic](https://docs.nginx.com/ngf/img/route-all-traffic-config.png)

Therefore, we need a Gateway to create an entry point for HTTP traffic coming into the cluster. The **cafe** Gateway we are going to create will open an entry point to the cluster on port 80 for HTTP traffic.

To route HTTP traffic from the Gateway to the coffee service, we need to create an **HTTPRoute** named coffee and attach it to the Gateway. This HTTPRoute will have a single routing rule that routes all traffic to the hostname "cafe.example.com" from the Gateway to the coffee service.

Once NGINX Gateway Fabric processes the cafe Gateway and coffee HTTPRoute, it will configure a data plane (NGINX) to route all HTTP requests sent to "cafe.example.com" to the pods that the coffee service targets:

![routing_flow](https://docs.nginx.com/ngf/img/route-all-traffic-flow.png)

```bash
# Get the cafe Gateway service and port
kubectl get svc -n nginx-gateway cafe-nginx

# NAME         TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
# cafe-nginx   LoadBalancer   10.97.98.153   <pending>     80:31344/TCP   3h9m

# Save gateway ip and port to terminal parameter
GW_IP=10.97.98.153
GW_PORT=31344
```

This Gateway is associated with NGINX Gateway Fabric through the **gatewayClassName** field.

We specify a **listener** on the Gateway to open an entry point on the cluster. In this case, since the coffee application accepts HTTP requests, we create an HTTP listener, named http, that listens on port 80.

```bash
kubectl apply -f - <<EOF
apiVersion: gateway.networking.k8s.io/v1
kind: HTTPRoute
metadata:
  name: coffee
spec:
  parentRefs:
  - name: cafe
  hostnames:
  - "cafe.example.com"
  rules:
  - matches:
    - path:
        type: PathPrefix
        value: /
    backendRefs:
    - name: coffee
      port: 80
EOF
```

## Test the configuration

```bash
curl --resolve cafe.example.com:$GW_PORT:$GW_IP http://cafe.example.com:$GW_PORT/

# Server address: 10.244.0.242:8080
# Server name: coffee-676c9f8944-jffdf
# Date: 09/Jul/2025:08:45:55 +0000
# URI: /
# Request ID: 18cfa04e87c2a3cf70ee04c46ea15787
```

Explain the `curl` command:

`--resolve` Flag

> This tells curl to use a custom hostname-to-IP resolution instead of DNS lookup. The format is: `--resolve <hostname>:<port>:<ip>`

1. When curl tries to connect to `cafe.example.com:$GW_PORT`
2. Instead of doing a DNS lookup for `cafe.example.com`
3. It uses the IP address from **$GW_IP**
4. But still sends the HTTP Host header as `cafe.example.com`

## TLS configuration

```bash
# Create a self signed certificate (key and )
openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
  -keyout example.com.key \
  -out example.com.crt \
  -subj "/CN=example.com" \
  -addext "subjectAltName = DNS:example.com,DNS:www.example.com"

# Create secret
kubectl create secret tls example.com --key example.com.key --cert example.com.crt

# Update gateway
kubectl apply -f - <<EOF
apiVersion: gateway.networking.k8s.io/v1
kind: Gateway
metadata:
  name: cafe
spec:
  gatewayClassName: nginx
  listeners:
  - name: http
    port: 80
    protocol: HTTP
  - name: https
    port: 443
    protocol: HTTPS
    # This is optional
    hostname: "cafe.example.com"
    tls:
      certificateRefs:
      - name: example.com
EOF

# Verify configuration
curl -k --resolve cafe.example.com:$GW_PORT:$GW_IP https://cafe.example.com:$GW_PORT/

# Server address: 10.244.0.248:8080
# Server name: coffee-676c9f8944-h4jv8
# Date: 09/Jul/2025:16:37:19 +0000
# URI: /
# Request ID: 3c004a6c0ba66bf7cebb3b503aa72b93
```
