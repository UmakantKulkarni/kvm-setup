# KVM short setup

Short tutorial for installing and running KVM supervisor and creating VMs with Ubuntu Cloud Image. 


## Requirements

Ubuntu-based computer with hardware virtualization support.

This tutorial actual for **Ubuntu 22.04 LTS**.

Check support (result should be more than 0):

```
egrep -c '(vmx|svm)' /proc/cpuinfo
```

## Installation

Install KVM related packages.

```
sudo apt install qemu-kvm virtinst libvirt-daemon-system cloud-image-utils
```

Check service libvirtd is running.

```
sudo systemctl status libvirtd
```

## Creating base OS image

Create directory for base OS images.

```
sudo mkdir /var/lib/libvirt/images/base
```

Get Ubuntu Minimal Cloud Image and move into directory.

```
wget https://cloud-images.ubuntu.com/minimal/releases/jammy/release/ubuntu-22.04-minimal-cloudimg-amd64.img
```

Check image has file format qcow2.

```
qemu-img info ubuntu-22.04-minimal-cloudimg-amd64.img
````

Move image into directory.

```
sudo mv ubuntu-22.04-minimal-cloudimg-amd64.img /var/lib/libvirt/images/base/ubuntu-22.04.qcow2
```

## Creating virtual machines

Get `createvm` from this repository and place into `~./local/bin` directory.

Also make it executable.

```
wget https://github.com/devgkz/kvm-short-setup/archive/refs/heads/main.zip
unzip main.zip
mv kvm-short-setup-main/createvm ~/.local/bin
chmod +x ~/.local/bin/createvm
```

Open `createvm` and check config block, edit if nesessary. 

By default script will create VM with 1vcpu, 512MiB RAM, 5G disk space, default "user" ans "pass". Also will be used your local key `~/.ssh/id_rda.pub` for authentication. Check it is exists.

Create new virtual machine, for example with id "vm1" and IP 192.168.122.101

```
createvm vm1 101
```

If IP not specified will be used automatic network config.

After creation, VM started automatically. Check it is running.

```
sudo virsh list

 Id   Name   State
----------------------
 1    vm1    running

```

Check the VM network configured properly.

```
sudo virsh domifaddr vm1  --source arp

 Name       MAC address          Protocol     Address
---------------------------------------------------------------
 vnet9      52:54:00:e1:4c:2c    ipv4         192.168.122.101/0

```

Now connect to VM via SSH.

```
ssh user@192.168.122.101
```


## Useful KVM commands

List of running VMs.
```
sudo virsh list
```

List all VMs.

```
sudo virsh list --all
```

Get console for vm1.

```
sudo virsh console vm1
```

Shutdown, reboot, start vm1.

```
sudo virsh shutdown vm1
sudo virsh reboot vm1
sudo virsh start vm1
```

Remove VM from supervisor.

```
sudo virsh undefine vm1
```

## Links, read more

Ubuntu Cloud Images https://cloud-images.ubuntu.com/

Cloud-init Docs https://cloudinit.readthedocs.io/

Ubuntu KVM/Networking https://help.ubuntu.com/community/KVM/Networking
