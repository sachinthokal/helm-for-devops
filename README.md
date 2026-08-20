# ⚙️ Helm for DevOps: Centralized Chart Registry & GitOps Workspace

![Helm](https://img.shields.io/badge/Helm-V3-blue?style=for-the-badge&logo=helm) ![Kubernetes](https://img.shields.io/badge/kubernetes-%23326ce5.svg?style=for-the-badge&logo=kubernetes&logoColor=white) ![ArgoCD](https://img.shields.io/badge/argo%20cd-%23ef7b4d.svg?style=for-the-badge&logo=argo&logoColor=white)

Welcome to the **Helm for DevOps** repository. This is a production-ready, centralized Helm artifact repository designed to host and manage Kubernetes package templates for microservices, using **GitHub Pages** as a private Helm registry and **ArgoCD** for GitOps continuous delivery.

---

## 🗂️ Repository Structure

The repository enforces a clean separation of concerns by isolating the raw Helm chart templates from the static packaged deployment artifacts (`.tgz`).

```text
Helm_For_Devops/
├── charts/                   # Source control for active Helm chart configurations
│   ├── helloworld/           # Demo boilerplate validation chart
│   └── spring-boot-helm-chart/ # Core chart for Java/Spring Boot Microservices
└── docs/                     # Production-Ready Artifacts (GitHub Pages root)
    ├── index.yaml            # Automatically generated Helm repository index
    ├── helloworld-0.1.0.tgz  # Packaged helloworld archive
    └── spring-boot-helm-chart-1.0.3.tgz # Packaged spring-boot archive

```

---

## 🚀 How to Add and Use This Registry

You can consume charts from this repository directly on any Kubernetes cluster globally.

### 1. Add the Helm Repository

```bash
helm repo add sachin-helm-repo https://sachinthokal.github.io/helm-for-devops

```

### 2. Update the Local Artifact Cache

```bash
helm repo update

```

### 3. Search Available Charts

```bash
helm search repo sachin-helm-repo

```

### 4. Direct Manual Installation

```bash
helm install my-spring-app sachin-helm-repo/spring-boot-helm-chart --version 1.0.3

```

---

## 🛠️ Chart Maintenance Workflow (For DevOps Engineers)

When modifications are introduced within the `charts/` directory, utilize the following sequence to package and publish the updates seamlessly to the registry.

### Step 1: Package the Target Chart

Compress the raw configuration source into the `docs/` tracking directory:

```bash
helm package ./charts/spring-boot-helm-chart/ -d docs/

```

### Step 2: Regenerate the Centralized Repository Index

Re-index the updated asset catalog natively within the hosted directory boundary:

```bash
helm repo index docs/

```

### Step 3: Ship the Changes to Production

Push the structural source and updated targets to GitHub to trigger the live GitHub Pages compilation:

```bash
git add .
git commit -m "chore: bumped chart release artifact version"
git push origin main

```

---

## 🔄 Declarative GitOps Deployment via ArgoCD

This repository serves as a dynamic helm source endpoint for GitOps agents. To manage cluster synchronization, use the following declarative **ArgoCD Application Manifest**:

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: spring-boot-app
  namespace: argocd
  labels:
    app.kubernetes.io/name: spring-boot-app
    app.kubernetes.io/part-of: argocd
spec:
  project: default
  source:
    repoURL: '[https://sachinthokal.github.io/helm-for-devops/](https://sachinthokal.github.io/helm-for-devops/)'
    chart: spring-boot-helm-chart
    targetRevision: '*' # Automatically evaluates and deploys the latest version updated in index.yaml
  destination:
    server: '[https://kubernetes.default.svc](https://kubernetes.default.svc)'
    namespace: default
  syncPolicy:
    automated:
      prune: true
      selfHeal: true

```

---

💡 **DevOps Best Practice Note:** Never expose plaintext application secrets inside the `values.yaml` hosted under `charts/`. Always leverage secure runtime injection models like Kubernetes External Secrets or Vault providers for production nodes.
