# Immich Setup

Here are the steps to get immich running using Helm.

NAME            CHART VERSION   APP VERSION     DESCRIPTION                                       
immich/immich   0.10.3          v2.0.0          A chart to power Immich (immich.app) running on...


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

## Step 2: Create the Database Credentials

Before bringing up the database, we need to securely store your credentials in the immich namespace.


```bash
kubectl create namespace immich

kubectl create secret generic immich-db-secret \
  --namespace immich \
  --from-literal=username=immich \
  --from-literal=password=SuperSecretImmichDBPass
```

## Step 3: Deploy the Official VectorChord Database

Now, we tell the CNPG operator to spin up your database using the official tensorchord/cloudnative-vectorchord image, mapping it directly to your nvme-fast storage.

Create a file named: *immich-db-cluster.yaml*

## Step 3: Custom values.yaml file

File *values.yaml*

## Step 4: Install Helm App

```bash
helm upgrade --install immich immich/immich --namespace immich -f values.yaml
```


## Step 5: Database Fix
Create extensions as root database user

```bash
kubectl exec -it -n immich immich-db-1 -- psql -U postgres -d immich -c "CREATE EXTENSION IF NOT EXISTS vector; CREATE EXTENSION IF NOT EXISTS vchord CASCADE; CREATE EXTENSION IF NOT EXISTS cube; CREATE EXTENSION IF NOT EXISTS earthdistance;"
```



