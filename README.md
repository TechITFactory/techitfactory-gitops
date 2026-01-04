# TechITFactory GitOps Repository

This repository contains Kubernetes manifests for deploying TechITFactory microservices using ArgoCD.

## Structure

```
├── apps/                      # ArgoCD Application manifests
│   ├── root-app.yaml         # Root app (App-of-Apps)
│   ├── frontend.yaml         # Frontend app definition
│   └── api-gateway.yaml      # API Gateway app definition
├── base/                      # Base Kubernetes manifests
│   ├── frontend/
│   │   ├── deployment.yaml
│   │   ├── service.yaml
│   │   └── kustomization.yaml
│   └── api-gateway/
│       ├── deployment.yaml
│       ├── service.yaml
│       └── kustomization.yaml
└── environments/              # Environment-specific overlays
    ├── dev/
    │   ├── frontend/
    │   ├── api-gateway/
    │   ├── ingress.yaml
    │   └── namespace.yaml
    └── prod/
        └── (similar structure)
```

## GitOps Workflow

```
1. Developer pushes code to techitfactory-app
2. CI builds Docker image and pushes to ECR
3. CI updates image tag in this repo
4. ArgoCD detects change and syncs to cluster
```

## Setup

### 1. Install ArgoCD
```bash
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

### 2. Apply Root Application
```bash
kubectl apply -f apps/root-app.yaml
```

### 3. Access ArgoCD UI
```bash
kubectl port-forward svc/argocd-server -n argocd 8080:443
# Open https://localhost:8080
```

### 4. Get Admin Password
```bash
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
```

## Updating Images

After CI builds a new image:
```bash
cd environments/dev/frontend
# Update kustomization.yaml with new image tag
```

ArgoCD will automatically detect and deploy the change.

## Environments

| Environment | Namespace | Replicas | Auto-Sync |
|-------------|-----------|----------|-----------|
| dev | techitfactory | 2 | Yes |
| prod | techitfactory-prod | 3 | Manual |
