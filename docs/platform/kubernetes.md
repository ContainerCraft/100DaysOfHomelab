# Kubernetes Undercloud | Intel Nuc(s) / Optiplex(s) / Etc

## Install Kubernetes
In this section we will install kubernetes onto the Fedora host(s) that we are using for the [Kargo Hypervisor](https://github.com/ContainerCraft/Kargo).

### 4. Deploy Kubespray
  a. Export Variables
```sh
export SSH_USER='kc2admin'
export SSH_PASS='kc2admin'
export HOSTS="192.168.1.51 192.168.1.52 192.168.1.53"
export VIRTUAL_IP="192.168.1.50"
export CLUSTER_DOMAIN="kubespray.home.arpa"
```
  b. Deploy Kubespray
```sh
touch /tmp/kubeconfig;
docker run -it --rm --pull always \
    --volume /tmp/kubeconfig:/config:z \
    -e KUBE_API_FQDN="api.${CLUSTER_DOMAIN}" \
    -e HOSTS="${HOSTS}" \
    -e VRRP_IP="${VIRTUAL_IP}" \
  quay.io/containercraft/konductor:kubespray -e crio_version="1.22" \
    --user ${SSH_USER} -e ansible_ssh_pass=${SSH_PASS} -e ansible_sudo_pass=${SSH_PASS}
```
#### 5) copy kubectl into kubeconfig path
```sh
mkdir -p ~/.kube && cp /tmp/kubeconfig ~/.kube/kubespray && chmod 600 ~/.kube/kubespray
```
#### 5) Point KUBECONFIG to new kubespray config && Test
```sh
export KUBECONFIG=~/.kube/kubespray
kubectl get po -A
```
#### 6) For Single, or 3 Node Clusters:
```sh
kubectl taint nodes --all --overwrite node-role.kubernetes.io/master-
kubectl label nodes --all --overwrite node-role.kubernetes.io/master=''
kubectl label nodes --all --overwrite node-role.kubernetes.io/control-plane=''
kubectl label nodes --all --overwrite node-role.kubernetes.io/worker=''
```
#### 5) Point KUBECONFIG to new kubespray config && Test
-------------------------------------------------
## OPTIONAL: Install Prometheus for Metrics
```sh
helm install kube-prometheus bitnami/kube-prometheus --namespace prometheus --create-namespace
```

-----
## Next: [Install Storage Provider(s)](./storage.md)
> ##### *This section also works on Raspberry Pi's
> ##### **These steps are also valid for just building a local kubernetes cluster without continuing further
