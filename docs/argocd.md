# Argo-CD

To overcome the chicken/egg problem, first install ArgoCD manually, with no ingress or anyhing.

```
helm repo add argo https://argoproj.github.io/argo-helm
helm install my-argo-cd argo/argo-cd --version 8.0.4
```
