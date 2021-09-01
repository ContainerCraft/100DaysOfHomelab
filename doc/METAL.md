# Kargo 2.0 KubeVirt Intel Nuc HomeLab

### 0. Join the ContainerCraft [CodeCtl Slack](https://join.slack.com/t/codectl/shared_invite/zt-szwjvpmc-RH_iObGnwSg1BVpXu79dAA)
### 1. Create/Have GitHub Account & Upload SSH Public Key
### 2. Download & Write Fedora 34+ to USB
### 3. Install Fedora
### 4. Configure Host Bridge br0 & Static IP(s)
  - each node should have a `br0` linux bridge interface
  - each node's `br0` interface should be the only default interface
  - each node's `br0` interface should be configured with a static address
### 5. Install ssh key from GitHub
```sh
mkdir ~/.ssh; chmod 700 ~/.ssh; chmod 600 ~/.ssh/authorized_keys
curl -L github.com/${GH_USERNAME}.keys | tee -a .ssh/authorized_keys
```
### 6. Prepare hosts for Kuberenetes Deploy
#### 6.a) Install Dependencies
```sh
sudo systemctl disable firewalld --now
sudo dnf remove -y zram-generator-defaults # disable swap
sudo dnf install -y openvswitch libibverbs openvswitch-devel NetworkManager-ovs keepalived haproxy dnf-automatic python3 python3-pip screenfetch glances lm_sensors htop tmux vim git tar
sudo sed -i 's/^apply_updates = no/apply_updates = yes/g' /etc/dnf/automatic.conf
sudo systemctl enable --now dnf-automatic.timer
sudo systemctl enable --now openvswitch
sudo python3 -m pip install --upgrade pip
python3 -m pip install --upgrade glances
sudo dnf -y update
```
#### 6.b) Install virtctl binary
```sh
export VIRTCTL_RELEASE=$(curl -s https://api.github.com/repos/kubevirt/kubevirt/releases/latest | awk -F '["v,]' '/tag_name/{print $5}')
sudo curl --output /tmp/virtctl -L https://github.com/kubevirt/kubevirt/releases/download/v${VIRTCTL_RELEASE}/virtctl-v${VIRTCTL_RELEASE}-linux-amd64
sudo install -o root -g root -m 0755 /tmp/virtctl /usr/local/bin/virtctl
```
#### 6.c) Install kubectl binary
```sh
curl --output /tmp/kubectl -L "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
sudo install -o root -g root -m 0755 /tmp/kubectl /usr/local/bin/kubectl
```
#### 6.d) Move ResolveD to port 5353 (required only on CentOS Workstation / Server with GUI
  - Export Variables on all nodes
```sh
export DNSIP=192.168.1.1
export VIPADDR=192.168.1.60
export IPADDR1=192.168.1.61
export IPADDR2=192.168.1.62
export IPADDR3=192.168.1.63
export CLUSTER="kargo"
```
```sh
cat <<EOF | sudo tee /etc/systemd/resolved.conf
[Resolve]
DNS=${DNSIP}
DNSStubListener=no
EOF
sudo mkdir -p /run/systemd/resolve && sudo ln -sf /run/systemd/resolve/resolv.conf /etc/resolv.conf
sudo systemctl disable dnsmasq ; sudo systemctl stop dnsmasq ; sudo pkill -KILL dnsmasq
sudo systemctl enable --now systemd-resolved
sudo systemctl restart systemd-resolved.service
sudo systemctl restart NetworkManager
```
#### 6.e) Create SSH Keys & Enable SSH to self
```sh
ssh-keygen -b 2048 -t rsa -f ~/.ssh/id_rsa -q -N ""
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys && chmod 600 ~/.ssh/authorized_keys
ssh $(ip a s $(ip r | awk '/default/{print $5}') | awk -F'[/ ]' '/inet /{print $6}' | head -n 1) whoami
```
#### 6.f) Configure HAProxy
```sh
cat <<EOF | sudo tee /etc/haproxy/haproxy.cfg
listen kubernetes-apiserver-https
bind *:8443
mode tcp
option log-health-checks
timeout connect         10s
timeout client          1m
timeout server          1m
server prod-master1 ${IPADDR1}:6443 check check-ssl verify none inter 10000
server prod-master2 ${IPADDR2}:6443 check check-ssl verify none inter 10000
server prod-master3 ${IPADDR3}:6443 check check-ssl verify none inter 10000
balance roundrobin
EOF
sudo systemctl enable --now haproxy ; sudo systemctl restart haproxy
sudo systemctl status haproxy
``` 
#### 6.h) Configure VRRP Virtual IP Address
  - master 1 (VRRP PRIMARY)
```sh
cat <<EOF | sudo tee /etc/keepalived/keepalived.conf
vrrp_script chk_haproxy {
  script "killall -0 haproxy"
  interval 2
  weight 2
}
vrrp_instance VI_1 {
  interface br0
  state MASTER
  advert_int 1
  virtual_router_id 51
  priority 101
  unicast_src_ip ${IPADDR1}    ##Master 1 IP Address
  unicast_peer {
      ${IPADDR2}               ##Master 2 IP Address
      ${IPADDR3}               ##Master 3 IP Address
   }
  virtual_ipaddress {
      ${VIPADDR}               ##Shared Virtual IP address
  }
  track_script {
    chk_haproxy
  }
}
EOF
```
  - master 2 (VRRP BACKUP)
```sh
cat <<EOF | sudo tee /etc/keepalived/keepalived.conf
vrrp_script chk_haproxy {
  script "killall -0 haproxy"
  interval 2
  weight 2
}
vrrp_instance VI_1 {
  interface br0
  state BACKUP
  advert_int 3
  virtual_router_id 50
  priority 100
  unicast_src_ip ${IPADDR2}    ##Master 2 IP Address
  unicast_peer {
      ${IPADDR1}               ##Master 1 IP Address
      ${IPADDR3}               ##Master 3 IP Address
   }
  virtual_ipaddress {
    ${VIPADDR}                 ##Shared Virtual IP address
  }
  track_script {
    chk_haproxy
  }
}
EOF
```
  - master 3 (VRRP BACKUP)
```sh
cat <<EOF | sudo tee /etc/keepalived/keepalived.conf
vrrp_script chk_haproxy {
  script "killall -0 haproxy"
  interval 2
  weight 2
}
vrrp_instance VI_1 {
  interface br0
  state BACKUP
  advert_int 3
  virtual_router_id 49
  priority 99
  unicast_src_ip ${IPADDR3}    ##Master 3 IP Address
  unicast_peer {
      ${IPADDR1}               ##Master 1 IP Address
      ${IPADDR2}               ##Master 2 IP Address
   }
  virtual_ipaddress {
    ${VIPADDR}                 ##Shared Virtual IP address
  }
  track_script {
    chk_haproxy
  }
}
EOF
```
```sh
sudo systemctl enable --now keepalived ; sleep 2; sudo systemctl restart keepalived ; sleep 2
sudo systemctl restart NetworkManager ; sleep 3
sudo systemctl status keepalived
ip a s br0
```
#### 6.f) Enable nested virtualization (SELINUX DISABLED -- LAB USE ONLY)
```sh
sudo grubby --update-kernel=ALL --args 'setenforce=0 selinux=0 cgroup_memory=1 cgroup_enable=cpuset cgroup_enable=memory systemd.unified_cgroup_hierarchy=0 intel_iommu=on iommu=pt rd.driver.pre=vfio-pci pci=realloc'
```
#### 6.h) Reboot
```sh
sudo reboot
```
#### 7) Prepare Kubespray ansible dependencies
  - Clone repo & install ansible packages
```sh
git clone https://github.com/kubernetes-sigs/kubespray.git ~/kubespray && cd ~/kubespray/
python3 -m pip install --upgrade -r requirements.txt
```
  - Export Variables on all nodes
```sh
export DNSIP=192.168.1.1
export VIPADDR=192.168.1.60
export IPADDR1=192.168.1.61
export IPADDR2=192.168.1.62
export IPADDR3=192.168.1.63
export CLUSTER="kargo"
```
  - Create ansible hosts inventory file
```sh
cp -rfp inventory/sample inventory/${CLUSTER}
declare -a IPS="${IPADDR1} ${IPADDR2} ${IPADDR3}"
CONFIG_FILE=inventory/${CLUSTER}/hosts.yaml python3 contrib/inventory_builder/inventory.py ${IPS[@]}
```
  - Write override variables to file
```sh
cat <<EOF > ${CLUSTER}-vars.yml
container_manager: crio
kube_encrypt_secret_data: true
kube_network_plugin_multus: true
kubelet_deployment_type: host
kubelet_shutdown_grace_period: 60s
kubelet_shutdown_grace_period_critical_pods: 20s
auto_renew_certificates: true
kubeconfig_localhost: true
etcd_deployment_type: host
download_container: true
kubectl_localhost: true
ping_access_ip: true
#######################################################
# Support HAPROXY & VRRP for High Availability
loadbalancer_apiserver_localhost: false
apiserver_loadbalancer_domain_name: "${CLUSTER}.codectl.arpa"
loadbalancer_apiserver:
  address: ${VIPADDR}
  port: 8443
#######################################################
# EXPERIMENTAL
# :w!
# curl -L https://raw.githubusercontent.com/alauda/kube-ovn/release-1.7/dist/images/install.sh | bash
#kube_network_plugin: kube-ovn
EOF
```
#### 8) Run ansible playbook
  - first task tests ssh access to all nodes
```sh
ansible -i inventory/${CLUSTER}/hosts.yaml -m ping all && time ansible-playbook -i inventory/${CLUSTER}/hosts.yaml --become --become-user=root --ask-become-pass --extra-vars @${CLUSTER}-vars.yml --user=fedora cluster.yml
```
#### 2.m) Link kubectl into path && Optimize for single node
```sh
mkdir -p ~/.kube && cp inventory/${CLUSTER}/artifacts/admin.conf ~/.kube/config && chmod 600 ~/.kube/config
kubectl patch node node1 -p '{"spec":{"taints":[]}}'
curl -L https://raw.githubusercontent.com/k8snetworkplumbingwg/multus-cni/master/images/multus-daemonset.yml | kubectl apply -f -
kubectl patch deployment -n kube-system coredns --patch='{"spec":{"template":{"spec":{"tolerations":[]}}}}'
kubectl -n kube-system rollout restart deployment/coredns
sleep 6
kubectl patch configmap -n kube-system dns-autoscaler --patch '{"data":{"linear":"{\"coresPerReplica\":256,\"min\":1,\"nodesPerReplica\":16,\"preventSinglePointFailure\":true}"}}'
kubectl patch storageclass hostpath-provisioner -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"true"}}}'
```
  - Wait for all pods to start up
```sh
watch kubectl get po -A
```
