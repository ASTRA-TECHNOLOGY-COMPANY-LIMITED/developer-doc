# Volumes and Data

## Overview

> A volume is a directory, possibly pre-populated, made available to containers in a Pod. The creation of the directory, the backend storage of the data and the contents depend on the volume type.

## Introducing Volumes

> A Pod specification can declare one or more volumes and where they are made available. Each requires a name, a type, and a mount point. The same volume can be made available to multiple containers within a Pod, which can be a method of container-to-container communication. A volume can be made available to multiple Pods, with each given an access mode to write. There is no concurrency checking, which means data corruption is probable, unless outside locking takes place.

![k8s_pod_volumes](https://d36ai2hkxl16us.cloudfront.net/course-uploads/e0df7fbf-a057-42af-8a1f-590912be5460/njibb9wicexq-KubernetesPodVolumes.png)

A particular access mode is part of a Pod request:

- `ReadWriteOnce` (RWO): The volume can be mounted as read-write once to a single node.
- `ReadOnlyMany` (ROX): The volume can be mounted as read-only to many nodes.
- `ReadWriteMany` (RWX): The volume can be mounted as read-write to many nodes.

> **NOTICE**: The `ReadWriteMany` access mode is not supported by all volume types.

## Volume Spec

One of the many types of storage available is an `emptyDir`. The kubelet will create the directory in the container, but not mount any storage, also, when the Pod is removed, the data in the emptyDir is deleted.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: fordpinto
  namespace: default
spec:
  containers:
  - image: simpleapp
    name: gastank
    command:
    - sleep
    - "3600"
    volumeMounts:
    - mountPath: /scratch
      name: scratch-volume
  volumes:
  - name: scratch-volume
    emptyDir: {}
```

The YAML file above would create a Pod with a single container with a volume named `scratch-volume` mounted at `/scratch`.

## Volume Types

### EmptyDir

> This is an empty directory that exists for the duration of the Pod's lifetime. It is created when the Pod is scheduled to a node and deleted when the Pod is removed from that node.

### HostPath

> This is a volume that mounts a file or directory from the host node's filesystem into the container. There are two types, `DirectoryOrCreate` and `FileOrCreate`, which create the directory or file if it does not exist.

## Shared Volume Example

The following YAML file creates a pod, `exampleA`, with two containers, both with access to a shared volume:

```yaml
....
  containers:
  - name: alphacont
    image: busybox
    volumeMounts:
    - mountPath: /alphadir
      name: sharevol
  - name: betacont
    image: busybox
    volumeMounts:
    - mountPath: /betadir
      name: sharevol
  volumes:
  - name: sharevol
    emptyDir: {}
```

> **NOTICE**: Note that one container (**betacont**) wrote, and the other container (**alphacont**) had immediate access to the data. There is nothing to keep the containers from overwriting each other's data. Locking or versioning considerations must be part of the containerized application to avoid corruption.

## Persistent Volumes and Claims

> A **persistent volume** (pv) is a storage abstraction used to retain data longer the Pod using it. Pods define a volume of type `persistentVolumeClaim` (pvc) with various parameters for size and `StorageClass`.

### Persistent Storage Phases

* **Provision**: **Provisioning** can be from PVs created in advance by the cluster administrator, or requested from a dynamic source, such as the cloud provider.
* **Bind**: **Binding** occurs when a control loop on the cp notices the PVC, containing an amount of storage, access request, and optionally, a particular `StorageClass`. The watcher locates a matching PV or waits for the `StorageClass` provisioner to create one. The binding process is completed when the PV is assigned to the PVC.
* **Use**: The **use** phase begins when the bound volume is mounted for the Pod to use, which continues as long as the Pod requires
* **Release**: **Releasing** happens when the Pod is done with the volume and an API request is sent, deleting the PVC. The volume remains in the state from when the claim is deleted until available to a new claim. The resident data remains depending on the `persistentVolumeReclaimPolicy` of the PV.
* **Reclaim**:
The **reclaim** phase has three options:
  * **Retain**: Keep data intact, allowing for an administrator to handle storage and data
  * **Delete**: Delete the volume, removing the data
  * **Recycle**: Run an `rm -rf /mountpoint` and then makes it available to a new claim (planned to be deprecated because of dynamic provisioning)

## Dynamic Provisioning

> Dynamic provisioning is a feature that allows the cluster to automatically create and manage storage volumes based on the storage class and access mode specified in the PVC.
> The `StorageClass` API resource allows an administrator to define a persistent volume provisioner of a certain type, passing storage-specific parameters.

Here is an example of a `StorageClass` using GCE:

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: fast
provisioner: kubernetes.io/gce-pd
parameters:
  type: pd-ssd
```

## Secrets

> Pods can access local data using volumes, but there is some data you don't want readable to the naked eye. Passwords may be an example. Using the Secret API resource, the same password could be encoded or encrypted.

Secrets can be encoded manually or via kubectl create secret:​

```bash
kubectl create secret generic mysql --from-literal=password=root
```

> A secret is not encrypted, only base64-encoded, by default. You must create an `EncryptionConfiguration` with a key and proper identity. Then, the kube-apiserver needs the `--encryption-provider-config` flag set to a previously configured provider, such as aescbc or kms. Once this is enabled, you need to recreate every secret, as they are encrypted upon write.
> Secrets are stored in the **tmpfs** storage on the host node, and are only sent to the host running Pod. All volumes requested by a Pod must be mounted before the containers within the Pod are started. So, a secret must exist prior to being requested.

## ConfigMaps

> A similar API resource to Secrets is the ConfigMap, except the data is not encoded. In keeping with the concept of decoupling in Kubernetes, using a ConfigMap decouples a container image from configuration artifacts.
> A ConfigMap can be used in several different ways. A container can use the data as environmental variables from one or more sources. The values contained inside can be passed to commands inside the pod. A Volume or a file in a Volume can be created, including different names and particular access modes. In addition, cluster components like controllers can use the data.
