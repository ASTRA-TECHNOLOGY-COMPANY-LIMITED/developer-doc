# High Availability

## Cluster High Availibility

> `kubeadm` has the ability to join multiple cp nodes with collocated etcd databases.

## Collocated Databases

The easiest way to gain higher availability is to use the `kubeadm` command and join at least two more cp servers to the cluster.

> **Note**: Should a node fail, you will lose both a control plane and a database

## Non-Collocated Databases

> Using an external cluster of etcd allows for less interruption should a node fail. Creating a cluster in this manner requires a lot more equipment to properly spread out services and takes more work to configure.

The external etcd cluster needs to be configured first. The `kubeadm` command has options to configure this cluster, or other options are available. Once the etcd cluster is running, the certificates need to be manually copied to the intended first control plane node.

> **Note**: The `kubeadm-config.yaml` file needs to be populated with the etcd set to external, endpoints, and the certificate locations
