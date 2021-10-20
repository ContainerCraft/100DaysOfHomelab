# TIPS
```sh
kubectl patch node node1 -p '{"spec":{"taints":[]}}'
kubectl scale deployment --replicas=0 dns-autoscaler --namespace=kube-system
kubectl patch deployment -n kube-system coredns --patch='{"spec":{"template":{"spec":{"tolerations":[]}}}}'
kubectl -n kube-system rollout restart deployment/coredns
sleep 6
kubectl patch configmap -n kube-system dns-autoscaler --patch '{"data":{"linear":"{\"coresPerReplica\":256,\"min\":1,\"nodesPerReplica\":16,\"preventSinglePointFailure\":true}"}}'
kubectl patch storageclass hostpath-provisioner -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"true"}}}'
curl -L https://raw.githubusercontent.com/k8snetworkplumbingwg/multus-cni/master/deployments/multus-daemonset.yml | kubectl apply -f -
```
```sh
## USE WITH EXTREME CAUTION
for i in $(sudo lvscan | grep -v fedora | awk '{print $2}' | sed "s/'//g"); do sudo lvremove -y $i; done
for i in $(sudo vgscan | grep -v fedora | awk '{print $4}' | sed 's/"//g'); do sudo vgremove -y $i; done
for i in $(sudo pvscan | grep -v fedora | awk '/dev/{print $2}'); do sudo pvremove -y $i; done
sudo wipefs -a /dev/sdb /dev/sdc /dev/nvme0n1
sudo sfdisk --delete /dev/sdb
sudo sfdisk --delete /dev/sdc
sudo sfdisk --delete /dev/nvme0n1
```
