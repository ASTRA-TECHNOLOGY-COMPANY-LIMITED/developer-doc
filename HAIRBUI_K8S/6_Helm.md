# Helm

## Chart Contents

> A **chart** is an archived set of Kubernetes resource manifests that make up a distributed application.

```bash
# Folder structure
├── Chart.yaml
├── README.md
├── templates
│   ├── NOTES.txt
│   ├── _helpers.tpl
│   ├── configmap.yaml
│   ├── deployment.yaml
│   ├── pvc.yaml
│   ├── secrets.yaml
│   └── svc.yaml
└── values.yaml
```

* **Chart.yaml**: This file contains some metadata about the Chart, like its name, version, keywords, and so on, in this case, for MariaDB.
* **values.yaml**: The **values.yaml** file contains keys and values that are used to generate the release in your cluster. These values are replaced in the resource manifests using the **Go** templating syntax.
* **template**: This directory contains the resources that make up this MariaDB application.

## Templates

> The templates are source manifests that use the Go templating syntax. Variables defined in `values.yaml`, get injected in the template when a release is created.

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: {{ template "fullname" . }}
  labels:
    app: {{ template "fullname" . }}
    chart: "{{ .Chart.Name }}-{{ .Chart.Version }}"
    release: "{{ .Release.Name }}"
    heritage: "{{ .Release.Service }}"
type: Opaque
data:
  mariadb-root-password: {{ default "" .Values.mariadbRootPassword | b64enc | quote }}
  mariadb-password: {{ default "" .Values.mariadbPassword | b64enc | quote }}
```

## Chart Repositories and Hub

> Repositories are currently simple HTTP servers that contain an index file and a tarball of all the Charts present. Prior to adding a repository, you can only search the [Artifact Hub](https://artifacthub.io/) using the `helm search hub` command.

```bash
helm search hub redis
```

You can interact with a repository using the helm repo commands

```bash
helm repo add bitnami ht‌tps://charts.bitnami.com/bitnami
helm repo list
# NAME      URL
# bitnami   ht‌tps://charts.bitnami.com/bitnami
```

Once you have a repository available, you can search for Charts based on keywords. Below, we search for a redis Chart:

```bash
helm search repo bitnami/redis
# NAME                    CHART VERSION   APP VERSION     DESCRIPTION
# bitnami/redis           21.2.4          8.0.2           Redis(R) is an open source, advanced key-value ...
# bitnami/redis-cluster   12.0.10         8.0.2           Redis(R) is an open source, scalable, distribut...
```

## Deploying a Chart

> To deploy a Chart, you can just use the `helm install` command. There may be several required resources for the installation to be successful, such as available PVs to match chart PVC. Currently, the only way to discover which resources need to exist is by reading the **READMEs** for each chart. This can be found by downloading the tarball and expanding it into the current directory

```bash
helm fetch bitnami/apache --untar
ls apache
# Chart.lock  Chart.yaml  README.md  charts  ci  files  templates  values.schema.json  values.yaml
helm install anotherweb .
```
