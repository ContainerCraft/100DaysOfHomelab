# Install Fedora
### 1. Download & Write Fedora 34+ to USB
  - [From Windows](https://www.youtube.com/watch?v=42vjjlhtufs)
  - [From MacOS](https://www.youtube.com/watch?v=f78AwZk3IXs)
  - [From Linux](https://www.youtube.com/watch?v=BCeG2JMuCpU)
    
### Hint: `sudo dd if=~/Downloads/$FEDORA_ISO of=/dev/sdX conv=sync status=progress`    
>  ###### * Where sdX is the name of your usb drive as seen in `$~ lsblk`
>  ###### **Be sure the USB is completely written before unpluging
### 2. [Install Fedora] (Video Coming Soon)
### 3. [Configure Host Bridge br0 & Static IP(s)](../../docs/hardware/Manual_br0.md)
  - each node should have a `br0` linux bridge interface
  - each node's `br0` interface should be the only default interface
  - each node's `br0` interface should be configured with a static address