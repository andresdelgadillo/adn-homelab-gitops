


## Cert Manager 
https://cert-manager.io/docs/tutorials/acme/nginx-ingress/


### Install Cert Manager with Helm
https://cert-manager.io/docs/installation/helm/


### Install repo
helm search repo jetstack/cert-manager

helm install \
  cert-manager oci://quay.io/jetstack/charts/cert-manager \
  --version v1.19.4 \
  --namespace cert-manager \
  --create-namespace \
  --set crds.enabled=true

## Create YAML file to edit values
Check letsencrypt-staging

kubectl create --edit -f https://raw.githubusercontent.com/cert-manager/website/master/content/docs/tutorials/acme/example/staging-issuer.yaml
# expected output: issuer.cert-manager.io "letsencrypt-staging" created
kubectl create --edit -f https://raw.githubusercontent.com/cert-manager/website/master/content/docs/tutorials/acme/example/staging-issuer.yaml
# expected output: issuer.cert-manager.io "letsencrypt-staging" created