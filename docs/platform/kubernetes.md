# Kargo 2.0 KubeVirt Intel Nuc HomeLab

### 0. Join the ContainerCraft [CodeCtl Slack](https://join.slack.com/t/codectl/shared_invite/zt-szwjvpmc-RH_iObGnwSg1BVpXu79dAA)
### 1. Download & Write Fedora 34+ to USB
  - [From Windows](https://www.youtube.com/watch?v=42vjjlhtufs)
  - [From MacOS](https://www.youtube.com/watch?v=f78AwZk3IXs)
  - [From Linux](https://www.youtube.com/watch?v=BCeG2JMuCpU)
    
Or `sudo dd if=~/Downloads/$FEDORA_ISO of=/dev/sdX conv=sync status=progress`
>  ###### * Where sdX is the name of your usb drive as seen in `$~ lsblk`
>  ###### **Be sure the USB is completely written before unpluging
### 2. Install Fedora
### 3. [Configure Host Bridge br0 & Static IP(s)](../../docs/hardware/Manual_br0.md)
  - each node should have a `br0` linux bridge interface
  - each node's `br0` interface should be the only default interface
  - each node's `br0` interface should be configured with a static address
### 4. Deploy Kubespray
  a. Export Variables
```sh
export SSH_USER='fedora'
export SSH_PASS='fedora'
export HOSTS="192.168.1.61"
export VIRTUAL_IP="192.168.1.60"
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
  quay.io/containercraft/konductor:kubespray \
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
-----
## Next: [Install Storage Provider(s)](./storage.md)