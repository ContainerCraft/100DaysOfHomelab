## Install Kargo Kubevirt Hypervisor Components    

  - Install Cert Manager
```sh
helm upgrade --install cert-manager jetstack/cert-manager --namespace cert-manager --create-namespace --set installCRDs=true
```
  - Install Network Addons Operator
```sh
helm upgrade --install cluster-network-addons ccio/cluster-network-addons --namespace cluster-network-addons --create-namespace
```
  - Install Kargo KubeVirt Components
```sh
kubectl create namespace kubevirt --dry-run=client -oyaml | kubectl apply -f -
helm upgrade --install kargo ccio/kargo --namespace kargo --create-namespace
kubectl label nodes --all node-role.kubernetes.io/kubevirt=""
```
    
---------------------------------
## Next: [Deploy Test Virtual Machines](./test.md)