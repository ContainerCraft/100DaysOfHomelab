# 100DaysOfHomelab
### Platform
  1. [Bare Metal Setup](docs/platform/metal.md)
  2. [Kargo Hypervisor](docs/platform/kargo.md)
  3. [Rook Ceph Storage](docs/platform/rook.md)
  4. [Deploy Virtual Machines](docs/platform/test.md)
### Middleware    
### Applications    
----------------
## Physical Architecture
![lasvg](https://raw.githubusercontent.com/ContainerCraft/100DaysOfHomelab/main/web/drawio/100Days.png)

## What is it?
Intended for both the new hobbiest and experienced DevOps professional.    
This set of guides & build tools is aimed at the Single Host comodity Nuc/Laptop/Desktop Cluster "HomeLab" Paradigm and can be expanded upon once the core fundamentals are practiced.    
    
By following these guides you will be able to:    
  1. Demostrate the significant potential of modest hardware 
  2. Improve your understandng and fluency in fitting common commercially relevant Open Source Software components together    
  3. Overcome barriers in consuming automation tools to improve your workflow beyond the burden of menial tasks    

## Purpose:
This tooling provides a common platform to quickly and seamlessly build declarative infrastructure and applications.    
    
The original inspiration for this project came from endless hours of testing different 
virtual network and virtual machine building tools and strategies in search of a paradigm
that meets a number of criteria included in the following.

#### The tooling should be consistent across hardware platforms including:
  + Client Laptops
  + Client Desktops
  + Low cost Home Labs
  + 100% Virtual Tenants
  + Operations Pre-prod & Test Environments

#### Easy end-user management and setup:
  + Easy to setup
  + Easy to manage
  + Logical to comprehend
  + Capable of multi-host clustering
  + Capable of nesting multiple layers of networks

#### Simple integration of technologies including:
  + Linux Containers
  + Kubernetes
  + OpenStack
  + KVM / Libvirt
  + Bare Metal Hosts
  + Physical Switching Gear