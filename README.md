# K3s Homelab

Using this repository as my main repo for labbing with K3s at home.

## Brainstorming

- Prometheus (metrics) - ✅
- Cert-manager (certificates if needed)
- CoreDNS (dns resolving) - ✅ (builtin with k3s)
- Traefik (ingress) - ✅ (builtin with k3s)
- Grafana (dashboard) - ✅
- OpenHAB (some home automation)
- ArgoCD (deployment) - ✅

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

## Kubeconfig

```bash
mkdir ~/.kube
sudo cp /etc/rancher/k3s/k3s.yaml ~/.kube/config
sudo chown $USER ~/.kube/config
chmod 600 ~/.kube/config
export KUBECONFIG=~/.kube/config
```

## Install Helm

```bash
curl https://baltocdn.com/helm/signing.asc | gpg --dearmor | sudo tee /usr/share/keyrings/helm.gpg > /dev/null
sudo apt-get install apt-transport-https --yes
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/helm.gpg] https://baltocdn.com/helm/stable/debian/ all main" | sudo tee /etc/apt/sources.list.d/helm-stable-debian.list
sudo apt-get update
sudo apt-get install helm
```

## Install ArgoCD

```bash

kubectl create namespace argocd

export KUBECONFIG=~/.kube/config

helm repo add argo https://argoproj.github.io/argo-helm
helm repo update
helm install argocd argo/argo-cd --namespace argocd --set configs.params."server\.insecure"=true --set redis.exporter.enabled=true --set redis.metrics.enabled=true --set server.metrics.enabled=true --set controller.metrics.enabled=true

kubectl get pods -n argocd

kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d

// Forward port

kubectl port-forward svc/argocd-server -n argocd 8080:80

```

## Kubernetes and Grafana

kubectl port-forward service/kube-prometheus-stack-prometheus -n kube-prometheus 9090:9090

