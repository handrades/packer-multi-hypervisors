# Overview

This project is going to contain multiple hypervisors packer builders that target some of the major OS used in the wild. I'll start with Proxmox and Hyper-V since they are the hypervisors I have currently available. You can use this project as a base for your packer builds.

## Getting started

To use this you will need:

* A Proxmox/Hyper-V hypervisor
* DVD ISO files
* [Packer](https://packer.io)

## Building an Image

You will first want to determine if your host running Packer can be accessed by the Proxmox host. This is because Packer will briefly run an http server so that the installer can download the kickstart file. If Proxmox is on the same network as your builder host and there are no other firewall restrictions on your builder host this should work fine. If not, you will need to copy/host the ks.cfg files on a publicly accessible server.

Next, you will need to know the URL to your Proxmox system, the name of the node to build on as well as a username and password of a user with sufficient privileges to create VMs and templates. If you have customized your install or are using a storage pool other than the default you will need to specify that as well. To build the CentOS 7 image would issue:

### Proxmox

```
packer build \
  -var proxmox_node=hk-pve1 \
  -var proxmox_username="root@pam" \
  -var proxmox_password=password \
  -var proxmox_url=https://192.168.0.11:8006/api2/json \
  centos7/packer.json
```

To build CentOS 8 issue:

```
packer build \
  -var proxmox_node=hp-pve \
  -var proxmox_username="root@pam" \
  -var proxmox_password=password \
  -var proxmox_url=https://192.168.0.11:8006/api2/json \
  centos8/packer.json
```

To build Ubuntu 20.04 issue:

```
packer build \
  -var proxmox_node=hp-pve \
  -var proxmox_username="root@pam" \
  -var proxmox_password=password \
  -var proxmox_url=https://192.168.0.11:8006/api2/json \
  ubuntu2004/packer.json
```

There are a number of other variables you can set. You will notice these closely match the available options for the [Proxmox builder](https://packer.io/docs/builders/proxmox.html). The full list of variables you can customize is:

* `proxmox_username` - username to log into Proxmox as
* `proxmox_password` - password to log into Proxmox as
* `proxmox_url` - URL of your Proxmox system
* `proxmox_node` - name of the Proxmox node to build on
* `proxmox_storage_pool` - name of the storage pool the image should be built on
* `proxmox_storage_pool_type` - type of storage pool, `lvm-thin` (default), `lvm` , `zfspool` or `directory`
* `proxmox_storage_format` - storage format, `raw` (default), `cow`, `qcow`, `qed`, `qcow2`, `vmdk` or `cloop` 
* `centos_image` - The CentOS DVD image. Defaults to the most recent release as of April 3, 2020
* `template_name` - Name of the template. Defaults to `CentOS7-Template` or `CentOS8-Template` depending on version
* `template_description` - Template description. Defaults to `CentOS 7 Template` or `CentOS 8 Template` depending on image being built.

## After the image is built

Once the image is built you will want to adjust any remaining settings in the template including creating a cloud-init drive. A cloud-init drive _must_ be created for you to ssh into any new VMs you create. For details on how to do so visit [https://blog.dustinrue.com/proxmox-cloud-init/](https://blog.dustinrue.com/proxmox-cloud-init/).
