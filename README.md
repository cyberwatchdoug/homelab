# homelab

This repository contains the [GitOps](https://www.gitops.tech/) configuration and documentation for my Kubernetes homelab. It is designed to manage, deploy, and maintain a set of applications and infrastructure components in a reproducible, declarative, and automated way.

## Table of Contents

- [Overview](#overview)
- [Repository Structure](#repository-structure)
- [Key Concepts](#key-concepts)
- [Tools Used](#tools-used)
- [How It Works](#how-it-works)
- [Environments](#environments)
- [Secrets Management](#secrets-management)
- [Adding or Updating Applications](#adding-or-updating-applications)
- [References](#references)

## Overview

This repo implements a GitOps workflow for my Kubernetes homelab cluster. Cluster state for apps, infrastructure, and configuration is defined as code and stored in this repository. Changes are automatically reconciled to the cluster using [Flux](https://fluxcd.io/), ensuring the cluster matches the desired state in Git.

---

## Repository Structure

```
README.md
apps/
  base/
    <app>/
      deployment.yaml
      service.yaml
      ...
  staging/
    <app>/
      kustomization.yaml
clusters/
  staging/
    apps.yaml
    infrastructure.yaml
    flux-system/
      gotk-components.yaml
      gotk-sync.yaml
      kustomization.yaml
infrastructure/
  base/
    <infra-component>/
      ...
  staging/
    <infra-component>/
monitoring/
```

- **apps/**: Application manifests and Kustomizations.
- **infrastructure/**: Infrastructure components (e.g., External Secrets).
- **clusters/**: Cluster-specific configuration and Flux bootstrapping.
- **monitoring/**: (Reserved for monitoring stack, e.g., Prometheus, Grafana).
  - *This is currently installed via Helm Chart commands but will be brought here soon.*

## Key Concepts

### GitOps

- **Declarative**: All desired state is described in YAML manifests.
- **Versioned**: Git is the source of truth; every change is tracked.
- **Automated Reconciliation**: Flux continuously ensures that my homelab cluster matches the state in Git.

### Kustomize

- Used for layering and customizing Kubernetes manifests for different environments (e.g., `base` vs. `staging`).

### Flux

- A GitOps operator that syncs Kubernetes resources from Git to the cluster.
- Manages both apps and infrastructure via [Kustomization](https://fluxcd.io/docs/components/kustomize/kustomization/) and [HelmRelease](https://fluxcd.io/docs/components/helm/helmreleases/) resources.

### External Secrets

- [External Secrets Operator](https://external-secrets.io/) integrates external secret stores (e.g., 1Password) with Kubernetes secrets.

## Tools Used

- **Kubernetes**: Specifically ***k3s*** container orchestration platform.
- **Flux**: GitOps operator for Kubernetes.
- **Kustomize**: Native Kubernetes manifest customization.
- **Helm**: Kubernetes package manager (used via Flux).
- **External Secrets Operator**: Syncs secrets from external stores.
- **(Todo) Monitoring Stack**: Prometheus and Grafana.

## How It Works

1. **Bootstrap**: Flux is installed and configured to watch this repository.
2. **Sync**: Flux controllers (source-controller, kustomize-controller, helm-controller, etc.) reconcile the manifests in `clusters/staging/` to the cluster.
3. **Apps & Infra**: Apps and infrastructure are defined in `apps/` and `infrastructure/`, layered with Kustomize for different environments.
4. **Secrets**: External Secrets Operator fetches secrets from external stores and injects them as Kubernetes secrets.

## Environments

- **base/**: Common configuration for all environments.
- **staging/**: Overlays and environment-specific configuration for the `staging` cluster.

## Secrets Management

Secrets are managed using [External Secrets Operator](https://external-secrets.io/):

- Define `ExternalSecret` resources (e.g., [`apps/base/linkding/secrets.yaml`](apps/base/linkding/secrets.yaml)) to map external secrets into Kubernetes.
- The operator fetches and syncs secrets from the configured secret store (e.g., 1Password).


## Adding or Updating Applications

1. **Add manifests** under `apps/base/<app>/`.
2. **Reference them** in the appropriate `kustomization.yaml`.
3. **Overlay** as needed in `apps/staging/<app>/`.
4. **Commit and push** changes to Git.
5. **Flux** will automatically reconcile the changes to the cluster.

## References

- [Flux Documentation](https://fluxcd.io/docs/)
- [Kustomize Documentation](https://kubectl.docs.kubernetes.io/references/kustomize/)
- [External Secrets Operator](https://external-secrets.io/)
- [GitOps Principles](https://www.gitops.tech/)

## License

MIT (or your preferred license)
