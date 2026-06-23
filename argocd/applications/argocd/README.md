# ArgoCD Setup

## Create Namespace & Add Repo

kubectl create namespace argocd
helm repo add argo https://argoproj.github.io/argo-helm
helm repo update

## Base Helm Installation

helm install argocd argo/argo-cd --namespace argocd

## Apply the Bootstrap Manifest

kubectl apply -f bootstrap.yaml

