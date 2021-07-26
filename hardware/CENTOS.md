# CentOS Stream 8 Install & Setup Guide

### 01. Download [CentOS Server] ISO Install File
[CentOS Server]:https://www.centos.org/centos-stream/
### 02. Create a Bootable USB Drive from the CentOS ISO
How To:
  - [Windows](https://docs.centos.org/en-US/centos/install-guide/Making_Media_USB_Windows/)
  - [Mac](https://docs.centos.org/en-US/centos/install-guide/Making_Media_USB_Mac/)
  - Linux (below)

  a. Attach USB to Linux system    
  b. In terminal, find the device path with    
```sh
  sudo lsblk
```
  c. Write ISO to USB with the following command    
```sh
  sudo dd if=~/Downloads/$NAME_OF_UBUNTU_ISO_FILE of=/dev/$DEVICE bs=1024k conv=sync status=progress
```
And now wait until the USB drive is done writing the file. The USB should blink
showing activity for a while after the command is run.

### 03. Install CentOS on all nodes
> NOTE: when planning an install on a system with more than one storage device 
> (Hard Drives, SSD Drives & NVME Drives) it is often good to keep your OS 
> installation separate from your workload storage. In the case of Kargo as a
> Hypervisor, once booted up, the OS should do minimal work and consume negligible
> space. Save the largest and fastest storage devices for later when we configure
> your Virtual Machine Storage Classes.

### 04. Configure a network bridge on all nodes
> NOTE: We are going to use a bridge interface named 'br0' as each node's primary
> network interface. Follow the examples to learn how.

[![asciicast](https://asciinema.org/a/ffiXIXpO2sjMEG2VOkyLeU8Jk.png)](https://asciinema.org/a/ffiXIXpO2sjMEG2VOkyLeU8Jk)
