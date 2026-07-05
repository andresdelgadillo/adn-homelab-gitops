# CloudNativePG Operator

## Step 1: Install the CloudNativePG Operator via Helm

First, we use Helm to install the operator that will manage our database.

```bash
helm repo add cnpg https://cloudnative-pg.github.io/charts
helm repo update

helm install cnpg cnpg/cloudnative-pg \
  --namespace cnpg-system \
  --create-namespace
```

## Check versions available to upgrade
helm search repo cnpg/cloudnative-pg --versions