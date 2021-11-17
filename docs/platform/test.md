
## Install Binaries
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

## Create ssh key file
  - Create Kubernetes secret with base64 encoded ssh pub key
```sh
ls ~/.ssh/id_rsa.pub >/dev/null || ssh-keygen
kubectl create secret generic kargo-sshpubkey-kc2user \
    --namespace kargo --dry-run=client -oyaml \
    --from-file=key1=$HOME/.ssh/id_rsa.pub \
  | kubectl apply -f -
```
  - Verify secret contents
```sh
kubectl get secret -nkargo -oyaml kargo-sshpubkey-kc2user | awk '/key1:/{print $2}' | base64 -d
```

## Create your first Virtual Machine(s)
  - Deploy Ephemeral NAT'd Ubuntu VM
```sh
kubectl apply -f https://raw.githubusercontent.com/ContainerCraft/Kargo/master/test/ubuntu-nat.yaml
```
  - Deploy Ephemeral Bridged Ubuntu VM
```sh
kubectl apply -f https://raw.githubusercontent.com/ContainerCraft/Kargo/master/test/ubuntu-br0.yaml
```
  - Deploy Persistent Bridged Ubuntu VM
```sh
kubectl apply -f https://raw.githubusercontent.com/ContainerCraft/Kargo/master/test/ubuntu-br0-persistent.yaml
```
## Create Live Migration enabled VMs
>    Note: the following virtual machines require the `ceph-filesystem-*` storage classes     
    
  - Deploy Persistent Live Migration Enabled Bridged Ubuntu VM
```sh
kubectl apply -f https://raw.githubusercontent.com/ContainerCraft/Kargo/master/test/ubuntu-br0-persistent-livemigrate.yaml
```
  - Deploy Persistent Live Migration Enabled Bridged Ubuntu & Fedora VMs with RDP enabled
```sh
kubectl apply -f https://raw.githubusercontent.com/ContainerCraft/Kargo/master/test/ubuntu-br0-persistent-livemigrate-rdp.yaml
kubectl apply -f https://raw.githubusercontent.com/ContainerCraft/Kargo/master/test/fedora-br0-persistent-livemigrate-rdp.yaml
```

## Tips:
  - Watch vm creation events
```sh
kubectl get events -Aw
```
  - List all Virtual Machines
```sh
kubectl get vm -A
```
  - List all Virtual Machine Instances (VMI) (Running VMs)
```sh
kubectl get vmi -A
```
  - Connect to different VMI Serial Console
>    FYI: Exit serial console with key combination `ctrl + shift + ]`
```sh
virtctl console -n kargo ubuntu
virtctl console -n kargo ubuntu-br0
virtctl console -n kargo ubuntu-br0-persistent
virtctl console -n kargo ubuntu-br0-persistent-livemigrate
virtctl console -n kargo ubuntu-br0-persistent-livemigrate-rdp
virtctl console -n kargo fedora-br0-persistent-livemigrate-rdp
```
  - Example ssh to bridge VMI with default user
>    FYI: get ip address from get vmi command
```sh
ssh kc2user@192.168.1.243
```

## FAQ:    
  - Q.1: Where do the vm images come from?
  - A.1: All images are built directly from upstream sources via [public opensource pipelines on Github](https://github.com/ContainerCraft/qubo/actions)    
        
    
  - Q.2: Can I change my ssh key after deploying virtual machines?
  - A.2: Yes, the qemu-guest-agent will update ssh keys on the fly, re-apply your ssh public key secret and it will rotate keys automatically