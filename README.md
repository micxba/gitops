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

- [https://www.talos.dev](Talos Kubernetes 1.20)
  - Control Plane Endpoint: https://192.168.0.216:6443
  - DNS Domain: cluster.local
  - Base Images
    - [Control Plane](https://factory.talos.dev/image/e8aa7ebcd209f69c863593d5c06ccab841ce3d90467f230a2d71737091e36edf/v1.10.0/metal-arm64.raw.xz)
    - [Worker Nodes](https://factory.talos.dev/image/555773d834dfc6ed7a6984256641c04bc7bfd2e85f03aa4d52a5745a2417479a/v1.10.0/metal-amd64.iso)
  - Machine Configurations
    - [Control Plane](docs/control-plane-mc.md)
    - [Worker Nodes](dcs/worker-node-mc.md)

## Bootstrap Software

- [Argo-CD](docs/argocd.md)
- Cert-Manager
- External-Secrets
- Headlamp
- Ingress NGINX
- KPS
- MetalLB
- NFS Provisioner
- Reflector
- Vault

