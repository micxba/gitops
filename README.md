# LAB Cluster setup

## Lab Cluster Infrastructure

### Control Plane

- Raspberry PI4B - 8GB Memory
  - ruleof3-cp01 - 192.168.0.216

### Worker Nodes

- IBM ThinkCentre Mini-PC's
  - ruleof3-w01 - 192.168.0.220
  - ruleof3-w02 - 192.168.0.156
  - ruleof3-w03 - 192.168.0.224

### Ancillary

- TP-Link 8-port swtich
- Synology DS-218+

## Kubernetes configuration

- [https://www.talos.dev](Talos Kubernetes 1.10 (CP currently running 1.10.1))
  - Control Plane Endpoint: https://192.168.0.216:6443
  - DNS Domain: cluster.local
  - Base Images
    - [Control Plane](https://factory.talos.dev/image/e8aa7ebcd209f69c863593d5c06ccab841ce3d90467f230a2d71737091e36edf/v1.10.0/metal-arm64.raw.xz)
    - [Worker Nodes](https://factory.talos.dev/image/555773d834dfc6ed7a6984256641c04bc7bfd2e85f03aa4d52a5745a2417479a/v1.10.0/metal-amd64.iso)
  - Machine Configurations
    - [Control Plane](docs/control-plane-mc.md)
    - [Worker Nodes](dcs/worker-node-mc.md)

## Bootstrap Software

- Argo-CD
  - > To overcome the chicken/egg problem, install Argo-CD via HELM first with `helm repo add argo https://argoproj.github.io/argo-helm
&& helm install my-argo-cd argo/argo-cd --version 8.0.4`.  Use port-forward to do initial setup etc.
  - **Dependencies**: 
    - HELM
- App of Apps
  - > While technically not a software it's the core resoure that will run in ArgoCD to 'install' other apps. It is located at `root-app.yaml` an needs to be insalled via `kubectl apply -f root-app.yaml -n argo-cd`
  - **Dependencies**:
    - kubectl
    - ArgoCD
- MetalLB
  - > MetalLB is used as my Load Balancer of choice. Allocate a free set of IP's on your router and specify them in the manifests/metallb.yaml file.
  - **Dependencies**:
    - ArgoCD
- External-Secrets
  - > The External Secrets Operator (ESO) is technically not a bootstrap requirement, however so many other apps rely on it where it just made good sense to me. There is another chicken/egg problem  when you want to specify the provider secret e.g. Cloudflare API token. For the initial setup create a secret manually and later on convert it to a ES after Vault is setup.
  - **Dependencies**:
    - ArgoCD
    - External-Secrets-Operator (circular)
    - Vault (if using ES)
- NFS Provisioner
  - > For PVC's I use NFS currently until I add additiinal NVME's to my worker nodes.
  - **Dependencies**:
    - ArgoCD
- Cert-Manager
  - **Dependencies**:
    - ArgoCD
    - External Secrets Operator
    - DNS Provider
- Ingress NGINX
  - > Default ingress class for my cluster. Initially install without default certification configuration unless Certificate Manager is already installed.
  - **Dependencies**:
    - ArgoCD
    - MetalLB
    - Cert-Manager

## First tier applications
- Headlamp
- Kube-Prometheus-Stack (KPS)
- Reflector
- Vault
- Dex

## Applications
- Bitwarden
  - > Not currently used. Initially tried for ESO alternative but it's too primitive in comparison.
- Argo Workflows / Argo Events
  - > Still a lot of work to be done but initial setup is working well. Initially I intended to have AWF be in the `argo-workflows` namespace, AE in the `argo-events` namespace and actual workflows be in the `workflows` namespace. While that setup is working it did requires a little RBAC trickery so I may revamp that at some point.
- Loki
  - > Logs TBC

## S3 Buckets for backup etc.

Using Minio container running on Synology, configured with OIDC auth. Currently the GUI does not appear to support lifecycle policies so you can do so via cli:
```sh
brew install minio/stable/mc
mc alias set s3 http://s3.sith.network:9000 $AWS_ACCESS_KEY_ID $AWS_SECRET_ACCESS_KEY
mc ilm add s3/etcd-backups --expire-days 14
mc ilm ls s3/etcd-backups
```