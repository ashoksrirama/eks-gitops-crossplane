# eks-gitops-crossplane
Repo to create EKS Clusters using Crossplane and bootstrap them with ArogCD

## Setup Crossplane

```bash
helm repo add crossplane-stable https://charts.crossplane.io/stable

helm repo update

kubectl create namespace crossplane-system

helm upgrade --install crossplane crossplane-stable/crossplane --namespace crossplane-system --create-namespace --wait
```

## Install k8s provider
```bash
kubectl apply -f config/provider-kubernetes.yaml
```

## Install the aws provider
```bash
kubectl apply -f config/provider-aws.yaml
```

## Create IRSA bindings
Follow instructions here -- https://github.com/crossplane/provider-aws/blob/master/AUTHENTICATION.md#using-iam-roles-for-serviceaccounts

## Install the provider config
```bash
kubectl apply -f config/provider-config-aws.yaml
```

## Install the custom compositions
```bash
kubectl apply -f config/config-eks.yaml

kubectl apply -f config/config-gitops.yaml
```

## Check the status and wait until all packages are healthy
```bash
kubectl get pkgrev
```

## Create an Instance of the Composition
```bash
kubectl create ns team-1

kubectl --namespace team-1 apply -f claims/aws-eks-gitops.yaml
```

## Check the status of the claims
```bash
kubectl --namespace team-1 get clusterclaims,gitopsclaims

kubectl get managed,releases
```

## Crossplane Compositions

Refer eks/ folder for EKS composition and gitops/ folder for ArgoCD composition

## Open Issues

1. OIDC Provider Config is not installed
2. Karpenter IRSA has to be manually setup due to helm formatting error and reschedule the pod
3. Supply the AWS Credentials to the Crossplane provider in the child cluster
4. Add necessary IAM mappings in aws-auth configmap of the child cluster
5. TBD...
