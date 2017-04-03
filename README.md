# Rancher

This repository contains the scripts, documentation and logic to prepare a self bootstraping Rancher cluster.

#### Install the Following

* VirtualBox >= 5.1.14
* VirtualBox Extension Pack
* Vagrant >= 1.9.3
    * vagrant-hostmanager
    * vagrant-vbguest
    * vagrant-share
* Ansible >= 2.2.1.0_2

```bash
./install_vagrant_plugins.sh
```

## Test a Box

Note that when you bring up a Virtual Box, the VirtualBox Guest Additions will be installed.  Obviously you would not do this in a bare metal environment, but this is needed in the development setting.  This is handled in the `Vagrantfile` so it's not a big deal.

On my machine it takes just under 10 minutes to provision a machine from scratch.  This is not normal in a VirtualBox/Vagrant config because we are actually doing a PXE install and starting from a blank .box file.  When you install from a normal .box file it is MUCH FASTER.  We are trying to emulate a bare metal environment as much as possible.

NOTE: You will have to enter your password for the `vagrant-hostmanager` to update the /etc/hosts file.

```bash
$ vagrant up r0.e.int --no-provision
```

Then you should be able to SSH to the box:

```bash
$ vagrant ssh r0.e.int
Welcome to Ubuntu 16.04.2 LTS (GNU/Linux 4.4.0-66-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage
Last login: Thu Mar 23 10:05:18 2017 from 10.0.2.2
ubuntu@rancher0:~$
```

## Vagrantfile

The `Vagrantfile` reads a data file called `nodes.yml`.  The following VMs are defined:

| Private IP     | Hostname | Base Box        | Description          |
|:---------------|:---------|:----------------|:---------------------|
| 172.28.128.101 | r0.e.int | ubuntu/xenial64 | Rancher Server/Agent |
| 172.28.128.102 | r1.e.int | ubuntu/xenial64 | Rancher Agent        |
| 172.28.128.103 | r2.e.int | ubuntu/xenial64 | Rancher Agent        |
| 172.28.128.104 | r3.e.int | ubuntu/xenial64 | Rancher Agent        |

## Rancher Server Setup

The `r0.e.int` VM is up, so let's provision it and get Rancher Server installed.

```bash
vagrant provision r0.e.int
```

You can then navigate to [r0.e.int](http://r0.e.int:8080)

Bam!  You have a Rancher Server running.

## Cluster Setup

Need to figure out how to get the agent registration docker command line from the running server.

sudo docker run -d --privileged -v /var/run/docker.sock:/var/run/docker.sock -v /var/lib/rancher:/var/lib/rancher rancher/agent:v1.2.1 http://172.28.128.101:8080/v1/scripts/38A60DA064048CF11FF9:1483142400000:6spywpeBE4qBthjBAvsh3sreaYY


http://172.28.128.101:8080/v1/scripts/2820F04E5C09AAC6C7AB:1483142400000:TYP0QHJykvA5ToFBPeNrd0lfv4


TBD

# References

[Ubuntu 16.10 Server](http://releases.ubuntu.com/16.10/ubuntu-16.10-server-amd64.iso)
[Ubuntu 16.10 Desktop](http://releases.ubuntu.com/16.10/ubuntu-16.10-desktop-amd64.iso)

[Ubuntu 16.04 Server](http://releases.ubuntu.com/16.04.2/ubuntu-16.04.2-server-amd64.iso)
[Ubuntu 16.04 Desktop](http://releases.ubuntu.com/16.04.2/ubuntu-16.04.2-desktop-amd64.iso)

[Ubuntu 15.04 Server](http://releases.ubuntu.com/14.04/ubuntu-14.04.5-server-amd64.iso)
[Ubuntu 15.04 Desktop](http://releases.ubuntu.com/14.04/ubuntu-14.04.5-desktop-amd64.iso)

[Centos 7 Mimimal](http://centos.host-engine.com/7/isos/x86_64/CentOS-7-x86_64-Minimal-1611.iso )

[SquashFS Kickstarting Ubuntu](http://askubuntu.com/questions/763363/pxe-setup-for-xenial-prepends-squashfs-path-with-cdrom)

# Random Notes I Took During Research

I did find that the Ubuntu 16.x releases no longer put the 1st network interface at `eth0` which was causing the `netcfg` step of the installation to SEGFAULT.  So I modified the kickstart file to use the interface that was detected, in this case `enp0s3`.  I found this [tidbit](http://www.itzgeek.com/how-tos/mini-howtos/change-default-network-name-ens33-to-old-eth0-on-ubuntu-16-04.html) on changing the default to `eth0` but that seems like a lot of work.