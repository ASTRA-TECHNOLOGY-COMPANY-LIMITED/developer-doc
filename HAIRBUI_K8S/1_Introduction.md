# Basics of Kubernetes

## What is Kubernetes?

According to the kubernetes.io website, Kubernetes is:

> "an open-source software for automating deployment, scaling, and management of containerized applications".

### Besides

- A key aspect of Kubernetes is that it builds on 15 years of experience at Google in a project called borg before being open-sourced. With over ten years of common production usage among organizations across various industries, it has become the standard choice for businesses.

- Google's infrastructure started reaching high scale before virtual machines became pervasive in the datacenter, and containers provided a fine-grained solution for packing clusters efficiently. Efficiency in using clusters and managing distributed applications has been at the core of Google challenges.

## Components of Kubernetes

![1_intro_k8s_component_go_lang](https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/g1uctx8amf8i-Course_TemplateBanners_lightbulb-large12.png)

Instead of using a large server, Kubernetes approaches the same issue by deploying many small applications, or microservices. Each microservice should be written to expect many possible agents available to respond to a request. It is also important that each microservice expects others to die and eventually be replaced, leading to a transient server deployment.

## Challenges

- Managing containers at scale and designing a secure distributed application based on microservices' principles may be challenging.

- A smart early step is deciding on a continuous integration/continuous delivery (CI/CD) pipeline to build, test and verify container images. Tools such as **Spinnaker**, **Jenkins** and **Helm** can be helpful to use, among other possible tools. This will help with the challenges of a dynamic environment and ensure containers meet minimum requirements.

## Kubernetes Architecture

![1_intro_k8s_architecture](https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/j0i2uejk3hr5-Kubernetes_Architecture.png)

In its simplest form, Kubernetes is made of one or three control plane nodes (aka cp nodes) and many worker nodes. The cp runs an API server, a scheduler, various operators and a storage system to keep the persistent state of the cluster, container settings, and the networking configuration.

Kubernetes exposes an API via the API server. You can communicate with the API using a local client called kubectl or you can write your own client and use curl commands. The kube-scheduler is forwarded the pod spec for running containers coming to the API and finds a suitable node to run those containers. Each node in the cluster runs two processes: a kubelet, which is often a systemd process, not a container, and kube-proxy. The kubelet receives the podSpec to run the containers, manages and downloads any resources necessary, and works with the local container engine to manage them on the local node. The local container engine could be containerd or some other.

The kube-proxy works with the network plugin to create and manage networking rules which may expose the container on the network to other containers inside the cluster or the outside world.

## Terminologies

| Term | Description |
| ---- | ----------- |
| Pod | A Pod consists of one or more containers which share an IP address, access to storage and namespace. Typically, one container in a Pod runs an application; if there are other containers, they support the primary application. |
| Operator | Each operator interrogates the kube-apiserver for a particular object state and spec. |
| Deployment | A Deployment does not directly work with pods. |
| ReplicaSets | The ReplicaSet is an operator which will create or terminate pods according to a podSpec. |
