# K3s Homelab

Using this repository as my main repo for labbing with K3s at home.

## Brainstorming

- Prometheus (metrics)
- Cert-manager (certificates if needed)
- CoreDNS (dns resolving)
- Traefik (ingress)
- Grafana (dashboard)
- OpenHAB (some home automation)
- Portainer (if necessary for some simplification)
- ArgoCD (deployment)

## Install and configure K3s

### Installation

[Source](https://lumochift.org/blog/k3s-argocd#installing-k3s)

```bash
// Install
curl -sfL https://get.k3s.io | sh -

// Check status
sudo systemctl status k3s
sudo k3s kubectl get nodes
```

## Install ArgoCD

```bash

sudo k3s kubectl create namespace argocd

helm repo add argo https://argoproj.github.io/argo-helm
helm repo update
helm install argocd argo/argo-cd --namespace argocd

sudo k3s kubectl get pods -n argocd

sudo k3s kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d

// Forward port

sudo k3s kubectl port-forward svc/argocd-server -n argocd 8080:443

```

## Consider app of apps pattern

[Source](https://argo-cd.readthedocs.io/en/stable/operator-manual/cluster-bootstrapping/)

