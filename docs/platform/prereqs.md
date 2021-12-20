# 100DaysOfHomelab Pre-requisites

## Local Host Setup
### Install Packages
  - Install [`helm`](https://kubernetes.io/docs/tasks/tools/)
```sh
curl -L https://git.io/JOWT2 | bash
helm version
```
  - Install [Kubernetes `kubectl`](https://kubernetes.io/docs/tasks/tools/)
```sh
export K8S_RELEASE_STABLE="$(curl -sSL https://dl.k8s.io/release/stable.txt)"
sudo curl --output /usr/local/bin/kubectl -L https://storage.googleapis.com/kubernetes-release/release/${K8S_RELEASE_STABLE}/bin/$(uname -s | awk '{print tolower($0)}')/amd64/kubectl
sudo chmod +x /usr/local/bin/kubectl
kubectl version --short --client
```
  - Install [Kubevirt `virtctl`](https://github.com/kubevirt/kubevirt/releases)
```sh
export VIRTCTL_RELEASE=$(curl -s https://api.github.com/repos/kubevirt/kubevirt/releases/latest | awk -F '["v,]' '/tag_name/{print $5}')
sudo curl --output /usr/local/bin/virtctl -L https://github.com/kubevirt/kubevirt/releases/download/v${VIRTCTL_RELEASE}/virtctl-v${VIRTCTL_RELEASE}-$(uname -s | awk '{print tolower($0)}')-amd64
sudo chmod +x /usr/local/bin/virtctl
virtctl version --client
```
### Add Helm Repositories
```sh
helm repo add ccio https://containercraft.io/helm
helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo add jetstack https://charts.jetstack.io
helm repo update
```
### Install [Kontena Lens](https://k8slens.dev/)