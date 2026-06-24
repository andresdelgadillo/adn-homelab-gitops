# Sealed Secrets

More details here: https://github.com/bitnami/sealed-secrets

Install the Sealed secret repo

```bash
helm repo add sealed-secrets https://bitnami.github.io/sealed-secrets
```

Search latest chart version, current latest 2.19.0

```bash
% helm search repo sealed-secrets
NAME                         	CHART VERSION	APP VERSION	DESCRIPTION
bitnami/sealed-secrets       	2.5.19       	0.31.0     	Sealed Secrets are "one-way" encrypted K8s Secr...
sealed-secrets/sealed-secrets	2.19.0       	0.38.1     	Helm chart for the sealed-secrets controller.
```

## How to Back Up the Private Key

Run this command from your laptop to fetch the master private key file:
Bash

```bash
kubectl get secrets -n kube-system \
  -l sealedsecrets.bitnami.com/sealed-secrets-key=active \
  -o yaml > master-private-key.yaml
```

## How to Restore It During a Rebuild

If your homelab blows up and you have to rebuild the entire Kubernetes cluster from scratch, here is your rescue plan:

Before you deploy your apps, open KeePass, copy your backup text, and save it back as master-private-key.yaml.

Apply it directly to your brand-new blank cluster:
Bash

```bash
kubectl apply -f master-private-key.yaml
```

Then let ArgoCD install the Sealed Secrets controller.


## How to use kubeseal to create secrets

### Step 1: Fetch your Cluster's Public Key

Run this command from your terminal to pull the public certificate down onto your machine. This file is safe to keep on your computer permanently.
Bash

```bash
kubeseal --fetch-cert \
  --controller-name=sealed-secrets \
  --controller-namespace=kube-system \
  > pub-cert.pem
```

### Step 2: Create your Plaintext Secret (Locally Only)

Create a temporary file named secret-raw-DO-NOT-UPLOAD.yaml.

    ⚠️ Important: Do not save or commit this file to your Git repository. We will delete it in a moment.

Notice that we use stringData instead of data. This allows you to type your password in plain text; kubeseal will handle the base64 formatting and the encryption altogether.
YAML

```bash
# Save as: secret-raw.yaml (Temporary)
apiVersion: v1
kind: Secret
metadata:
  name: grafana-admin-credentials
  namespace: observability # Must match Grafana's namespace
type: Opaque
stringData:
  admin-password: YourSuperSecurePasswordHere # Put your real password here
```

### Step 3: Run kubeseal to Encrypt the Secret

Run this command to process your plaintext secret against your cluster's public key certificate. It will output a completely secure SealedSecret manifest:

```bash
kubeseal --cert ~/.ssh/pub-cert-seal-secrets.pem --format yaml < secret-raw-DO-NOT-UPLOAD.yaml > grafana-sealed-secret.yaml
```

Immediately and permanently delete your plaintext file:

```bash
rm secret-raw.yaml
```

### Step 4: Review the Encrypted Output

If you open your newly generated sealed-secret.yaml, it will look completely obscured:

```yaml
apiVersion: bitnami.com/v1alpha1
kind: SealedSecret
metadata:
  creationTimestamp: null
  name: grafana-admin-credentials
  namespace: observability
spec:
  encryptedData:
    admin-password: AgBy8hCK...[A massive wall of encrypted unreadable text]...==
```

This encrypted ciphertext can only be unlocked by the private key inside your Orange Pi's KeePass-backed vault. It is completely safe to expose publicly on GitHub.