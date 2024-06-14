# Mastering Kubernetes - Self-Services with Crossplane

Code snippets used in the talk about Kubernetes Self-Services with Crossplane.


The examples are using Azure Cloud, but can be adapted to all other cloud providers.

See the getting started docs at https://docs.crossplane.io/v1.16/getting-started/

## Prerequisites

A Kubernetes Cluster is required. We can use [KIND](https://kind.sigs.k8s.io/) for that:

```bash
kind create cluster
```

```bash

We use an Azure Service Principal to provision stuff in Azure Cloud:

```bash
az ad sp create-for-rbac \
  --sdk-auth \
  --role Contributor \
  --scopes /subscriptions/<your-subscription-id>

kubectl create namespace crossplane-system
kubectl create secret \
  generic azure-secret \
  -n crossplane-system \
  --from-file=creds=./azure-credentials.json
```

## Install Crossplane

```bash
helm repo add crossplane-stable https://charts.crossplane.io/stable
helm repo update
helm install crossplane \
  crossplane-stable/crossplane \
  --namespace crossplane-system \
  --create-namespace
```

## Live Demo

1. show installed crossplane pods
2. show installed Crossplane CRDs: `kubectl api-resources | grep crossplane`
3. Install Azure Provider: `kubectl apply -f crossplane/provider.yaml`
- list of all providers: https://marketplace.upbound.io/providers/upbound/provider-family-azure/v1.3.0/docs
- List Azure resources: `kubectl api-resources | grep azure`
4. Create ProviderConfig: `kubectl apply -f crossplane/providerconfig.yaml`
5. Install patch+transform function: `kubectl apply -f crossplane/function-patch-and-transform.yaml`
6. Create managed resource (Resource Group, https://marketplace.upbound.io/providers/upbound/provider-family-azure/v1.3.0/resources/azure.upbound.io/ResourceGroup/v1beta1)
7. Create a XRD `kubectl apply -f crossplane/composition/xrd.yaml`
   => CRD created `kg crd | grep account`
8. Create the matching composition `kubectl apply -f crossplane/composition/composition.yaml`
9. Create a claim: `kubectl apply -f crossplane/composition/claim.yaml`
