### Configure a network bridge on all nodes

The Kargo Kubevirt hypervisor and most of the applications and tenant vm's created by containercraft are dependent on host networking built around the br0 network interface. This network interface (nic) is a "virtual" nic and not the actual of your physical ethernet ports.

Normally you will configure this while you install Fedora. This guide is provided if you should need to configure the br0 virtual interface *after* installing Fedora.

If you cannot run the `nmtui` command, then install the required package and try again.
```
sudo dnf install -y NetworkManager-tui
```

Follow the example to learn how.

[![asciicast](https://asciinema.org/a/ffiXIXpO2sjMEG2VOkyLeU8Jk.png)](https://asciinema.org/a/ffiXIXpO2sjMEG2VOkyLeU8Jk)

Another recording can be found [HERE](./web/create_br0.svg)
