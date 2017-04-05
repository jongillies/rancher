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

## Run Rancher

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

## Rancher Server Setup

The `r0.e.int` VM is up, so let's provision it and get Rancher Server installed.

```bash
vagrant provision r0.e.int
```

You can then navigate to [r0.e.int](http://r0.e.int:8080)

Bam!  You have a Rancher Server running.

## Cluster Setup

Now run `vagrant up` to bring up the rest of the nodes.


![screenshot](files/screenshot.png)