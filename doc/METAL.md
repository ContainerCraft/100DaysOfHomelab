# Kargo 2.0 KubeVirt Intel Nuc HomeLab

### 0. Join the ContainerCraft [CodeCtl Slack](https://join.slack.com/t/codectl/shared_invite/zt-szwjvpmc-RH_iObGnwSg1BVpXu79dAA)
### 1. Download & Write Fedora 34+ to USB
### 2. Install Fedora
### 3. Configure Host Bridge br0 & Static IP(s)
  - each node should have a `br0` linux bridge interface
  - each node's `br0` interface should be the only default interface
  - each node's `br0` interface should be configured with a static address
### 4. Deploy Kubespray
  - Export Variables
```sh
export SSH_USER='fedora'
export SSH_PASS='fedora'
export HOSTS="192.168.1.61"
export VIRTUAL_IP="192.168.1.60"
export CLUSTER_DOMAIN="kubespray.home.arpa"
```
  - Deploy Kubespray
```sh
touch /tmp/kubeconfig;
docker run -it --rm --pull always \
    --volume /tmp/kubeconfig:/config:z \
    -e KUBE_API_FQDN="api.${CLUSTER_DOMAIN}" \
    -e HOSTS="${HOSTS}" \
    -e VRRP_IP="${VIRTUAL_IP}" \
  quay.io/containercraft/konductor:kubespray \
    --user ${SSH_USER} -e ansible_ssh_pass=${SSH_PASS} -e ansible_sudo_pass=${SSH_PASS}
```
  - Wait for all pods to start up
```sh
watch -c 'KUBECONFIG=/tmp/kubeconfig kubectl get po -A'
```
#### 5) Link kubectl into path && Optimize for single node
```sh
mkdir -p ~/.kube && cp /tmp/kubeconfig ~/.kube/kubespray && chmod 600 ~/.kube/kubespray
```
