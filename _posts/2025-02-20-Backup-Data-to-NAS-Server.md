---
layout: post
date: 2025-02-20
tag: IT
categories: [Bioinformatics]
---
We have a huge amount of sequencing data stored on the cluster server. However, we should not keep data there permanently. Instead, our group has its own NAS server where data should be stored. This post explains how to transfer data both from a personal computer and from the cluster to the NAS.  
<!--more-->

### 1. Accessing the NAS Server  

First, create a new account on the NAS if you haven’t already. Then, log in via the command line:  

```shell
ssh user@ip_address  # Example: ssh Emma@10.227.148.70
```

After entering your password, you will gain access. Note that only users in the admin group can log in via SSH. Once logged in, you can execute any Linux commands as usual.

### 2. Transferring Data from the Cluster Server to the NAS

To transfer data from the cluster (EVE server) to our NAS, we need to configure SSH access properly.


#### Step 1: Set Up SSH Configuration
Run the following commands in the terminal:
```shell
mkdir -p ~/.ssh
chmod 0700 ~/.ssh
vim ~/.ssh/config
```
Then, add the following lines to `~/.ssh/config`:

In the ~/.ssh/config put in:

```plaintext
Host frontend1.eve.ufz.de frontend2.eve.ufz.de
    ProxyCommand ssh idiv-gateway.ufz.de -W %h:%p

Host idiv-gateway.ufz.de
    ControlPath ~/.ssh/cm-%r@%h:%p
    ControlMaster auto
    ControlPersist 15m
    LocalForward localhost:8022 frontend1.eve.ufz.de:22
    LocalForward localhost:8023 frontend2.eve.ufz.de:22

Host *.eve.ufz.de
    ProxyCommand ssh frontend1.eve.ufz.de -W %h:%p

Host *.ufz.de
    User jiangc  # put your user name on eve cluster
```

#### Step 2: Transfer Data Using scp
After setting up the SSH configuration, you can transfer data from the EVE cluster to the NAS using scp.

Example command:
```shell
scp jiangc@frontend1.eve.ufz.de:/data/sp11/rawData/rawData2025I/F001_2/X208SC24071620-Z01-F001_2.tar ./
```

### 3. Transferring Data Between Your PC and NAS

You can transfer files between your personal computer and the NAS server either using a graphical interface (GUI) or via the command line.

Example `scp` command:
```shell
scp text.txt Emma@10.227.148.70:/var/services/homes/Emma/test/
```
This command transfers text.txt to the test folder in Emma’s home directory on the NAS.