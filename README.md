# GitOps Vault Deployment

> ArgoCD pulls from this repo to deploy Vault to Kubernetes

## Structure

```
gitops-vault/
├── README.md
├── apps/
│   └── vault/
│       ├── Chart.yaml    # Umbrella chart (depends on upstream)
│       └── values.yaml   # Your custom configuration
└── argocd-app.yaml       # ArgoCD Application manifest
```

## GitOps Flow

```
1. You commit changes to this repo
        ↓
2. ArgoCD detects changes (polls or webhook)
        ↓
3. ArgoCD renders Helm chart with your values
        ↓
4. ArgoCD applies to cluster (creates native K8s resources)
        ↓
5. Vault runs in cluster
```

## What ArgoCD Creates (Native Resources)

```
Helm Chart (vault)
    ↓ renders
├── StatefulSet/vault-0      # Vault server pod
├── Service/vault            # Internal access
├── Service/vault-ui         # UI access
├── ServiceAccount/vault     # Pod identity
├── ConfigMap/vault-config   # Configuration
└── ClusterRoleBinding       # RBAC
```

## Inspect Native Resources

```bash
# See all resources ArgoCD created
kubectl get all -n vault

# Detailed view
kubectl describe statefulset vault -n vault

# Logs
kubectl logs -n vault vault-0
```

## Setup Instructions

1. Push this repo to GitHub
2. Apply the ArgoCD Application:
   ```bash
   kubectl apply -f argocd-app.yaml
   ```
3. Watch sync in ArgoCD UI or:
   ```bash
   kubectl get applications -n argocd
   ```

## Access Vault

```bash
# Port forward
kubectl port-forward svc/vault -n vault 8200:8200

# Access UI: http://localhost:8200
# Token: root (dev mode)
```
