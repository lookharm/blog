+++
date = '2026-03-30T21:07:25+07:00'
title = 'So2 Labs Start Up Script'
tags = ['shell', 'docker']
+++

The shell script `local.sh` is responsible for setup environment for the labs.
It pulls a docker image which is very big (1.6 GB), creates a volume and runs a docker container.

Some useful utils that is used by local.sh:

**cut**
- *cut* is a useful GNU core utils, it print selected parts of lines, work like CSV, we can set delimiter and select which parts we want to display.
- e.g. the text `read,write,read/write`
- `... | cut -d , -f 1,2`   output = read,write
- `... | cut -d , -f 1-3`   output = read,write,read/write

**docker**
- `--privileged` mode this is very unsafe command to run as it disables the container's isolation and security limits. All host privileges belong to container.
- This is require because of KVM (/dev/kvm). Grants the container extended capabilities, necessary for KVM to access host kernel-level virtualization.

## Links
* https://gitlab.cs.pub.ro/so2/so2-labs
* https://learn.snyk.io/lesson/container-runs-in-privileged-mode/?ecosystem=kubernetes
