vmdeploy
========


This playbook is used to deploy vm quickly on Qemu/KVM with a minimal ip/dns
setup to allow further configuration via ansible. It uses cloud images with
NoCloud as datasource. No metadata server is used. Data are injected into
image before booting.

----------

Features
--------

- latest distro cloud images (amd64 family).
- Verify gpg signature and image checksum.
- use cloud-init.
- build with virt-install.
- extract kernel and initrd files from image to boot in kernel context.
- provide root access to user (via sudo and ssh key).
- allow deployment on remote compute.

Inputs
------

- vm name
- disk sizing
- static ip/dns with one network interface (eth0 ipv4 only).
- user + ssh key
- additional interfaces allowed.
- remote KVM host.

Problems
--------

Cloud-init is not fully supported by all distros and support varies from
version to version. Centos still uses an old version of cloud-init (0.7.5)
which is buggy on this platform. As a result, dns support is incomplete.
Debian does not allow static IP.
Cirros image allows to setup the hostname only.

The playbook attempts to fix bugs and provides missing features.

Tested on
---------

* ubuntu20.04 with ansible:2.9.6, python:3.8.2 (standard packages)
Programs requirements
---------------------

ansible
gpg
xz
guestfish
qemu-img
virt-install
xmlstarlet

>**Notes**
- If you want to use virt-manager as console, remember to install xauth.
- On remote deployment, you have to provide an account which is member of the 'libvirt' group.

Create the facts directory:
mkdir /etc/ansible/facts.d

Add a standard bridge br0 on the hypervisor and no nat.
See also virt-install in the bootstrap role.

Ansible deps
------------

- python3-libvirt
- python3-netaddr

Supported systems
-----------------

- ubuntu 14 16 17 20 22
- debian 9 10 <sup>1</sup>
- Centos 7
- Rocky 8
- cirros <sup>1</sup> :)

<sup>1</sup> note :
- debian 10 is deployed as debian 9 (see osinfo-query os)
- cirros is deployed as 0.3.5

Usage
-----

```sh
ansible-playbook -i inventory playbook_install.yml [ -t tags ] [ -e debug=1 ]

debug=1 :
- keep working directory
- set password for root and nadmin

tags:
- setup : download image and all related stuff.
- bootstrap : build instance and launch it.


ansible-playbook -i inventory playbook_install.yml -e update_facts=1

Update facts about distrib if removed.
```

To check deployment
-------------------

```sh
ansible-playbook -i inventory playbook_check.yml [ -e debug=1 ]

debug=1 :
- display inventory host facts.
```

Next step ?
---------

Use your playbooks for configuration...
