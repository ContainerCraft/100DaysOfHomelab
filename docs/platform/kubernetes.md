# Kubernetes Undercloud | Intel Nuc(s) / Optiplex(s) / Etc

## Install Kubernetes
In this section we will install kubernetes onto the Fedora host(s) that we are using for the [Kargo Hypervisor](https://github.com/ContainerCraft/Kargo).

### 1. Download & Write Fedora 34+ to USB
  - [From Windows](https://www.youtube.com/watch?v=42vjjlhtufs)
  - [From MacOS](https://www.youtube.com/watch?v=f78AwZk3IXs)
  - [From Linux](https://www.youtube.com/watch?v=BCeG2JMuCpU)
    
Or `sudo dd if=~/Downloads/$FEDORA_ISO of=/dev/sdX conv=sync status=progress`
>  ###### * Where sdX is the name of your usb drive as seen in `$~ lsblk`
>  ###### **Be sure the USB is completely written before unpluging
### 2. [Install Fedora] (Video Coming Soon)
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
> ##### *This section also works on Raspberry Pi's
> ##### **These steps are also valid for just building a local kubernetes cluster without continuing further