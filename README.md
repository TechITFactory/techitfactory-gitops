# TechIT Factory - GitOps

ArgoCD Application manifests - Single Source of Truth for cluster state.

## Structure
├── apps/ │ ├── platform/ # ArgoCD, Prometheus, Loki, Grafana │ └── services/ # App Helm releases ├── environments/ │ ├── dev/ # Dev values overrides │ └── prod/ # Prod values overrides └── app-of-apps.yaml # Root ArgoCD application


## How It Works
1. ArgoCD watches this repo
2. Changes here = Changes in cluster
3. Git history = Audit log

## Promotion Model
- Merge to `main` → Deploys to `dev` (auto-sync)
- Git Tag/Release → Deploys to `prod` (manual sync)

## Rollback
```bash
git revert <commit>
git push
# ArgoCD auto-syncs the rollback
