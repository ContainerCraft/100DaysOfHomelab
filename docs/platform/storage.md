# Storage    
Persistent storage for your applications must be provided by one or more Storage Provisioners. Each storage provisioner allocates physical storage into logical Storage Classes as the foundational component understandable by your cluster.    
    
> NOTICE:    
> - Only set one storage class as your default storage class 
> - Below options are examples and can be replaced with other storage providers 

---
## Install Storage Provider
  - (Option 1) Hostpath Provisioner 
>  Recommended for all installations
```sh
helm upgrade --install hostpath-provisioner ccio/hostpath-provisioner --namespace hostpath-provisioner --create-namespace
```
  - (Option 2) Rook Ceph
>  Recommended for >=3 node clusters with extra empty HDD/SSD/NVME disks
```sh
helm upgrade --install calypso ccio/calypso --namespace rook-ceph --create-namespace
```

---
## Set default storage class
>    Set only one storage class as default
  - Execute to make the storage class `hostpath-provisioner` your default storage class
```sh
kubectl patch storageclass hostpath-provisioner -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"true"}}}'
```
  - Execute to make the storage class `ceph-filesystem-ssd` your default storage class
```sh
kubectl patch storageclass ceph-filesystem-ssd -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"true"}}}'
```

---
## Next: [Deploy Kargo Hypervisor Stack](./kargo.md)     
    
## Further Reading
  - [Rook Ceph Docs](https://rook.github.io/docs/rook/latest)
  - [Hostpath Provisioner](https://github.com/kubevirt/hostpath-provisioner)
  - [Kubernetes.io - Storage Classes](https://kubernetes.io/docs/concepts/storage/storage-classes)
