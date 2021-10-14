  - Add ContainerCraft Helm Repo
```sh
helm repo add ccio https://containercraft.io/helm/
```
  - (Example) Install Default Storage
```sh
helm install calypso ccio/calypso --namespace rook-ceph --create-namespace
```
  - Install Network Addons Operator for Multus & other network plugins
```sh
helm install cluster-network-addons ccio/cluster-network-addons --namespace cluster-network-addons --create-namespace
```
  - Install Kargo KubeVirt Components
```sh
helm install kargo ccio/kargo     --namespace kargo --create-namespace
```
