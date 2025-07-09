# Kustomize

> Kustomize was created to overcome the limitations of using pure templating systems, which often require duplicating YAML files or using complex templating logic.

> **NOTICE**: Unlike tools such as Helm, Kustomize does not rely on templating languages to inject variables into your YAML. Instead, it uses strategic merging and patches to adjust configurations.

Kustomize is now a built-in feature of kubectl. This integration offers a streamlined workflow for deploying your applications. This integration allows you to run commands like `kubectl kustomize dir` to view the Kustomized YAML and `kubectl apply -k dir` to apply it to your cluster.

## Concept

### Directory Structure

```bash
├── base
│   ├── deployment.yaml
│   ├── ingress.yaml
│   ├── pvc.yaml
│   ├── secrets.yaml
│   └── svc.yaml
│   └── kustomization.yaml
├── overlays/
│   ├── dev/
│   │   └── kustomization.yaml
│   └── prod/
│       └── kustomization.yaml
```

### kustomization.yaml

> `kustomization.yaml` is a key configuration file used by Kustomize to customize and manage Kubernetes YAML manifests.

- Allow to apply environment-specific changes (dev, stag, prod) without duplicating YAML files
- `overlays` let you modify parts of your configuration (e.g., image versions, labels) on top of the base setup.
- You can automatically add prefixes or suffixes to resource names to differentiate between environments. For instance, adding a prefix `- namePrefix: lf-`. This would transform resource named `myapp-deployment` into `lf-myapp-deployment`.
- Adds common labels or annotations to all the resources defined
- Generates ConfigMaps or Secrets from files or literal values
- Applies patches to the base resources, enabling you to override or modify specific fields without altering the original files.
