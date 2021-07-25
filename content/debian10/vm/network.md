---
title: "Network"
date: 2021-07-24T17:27:05-04:00
draft: true
---

## Windows hosts

```PowerShell
# As admin
notepad C:\Windows\System32\drivers\etc\hosts

```

## Set Static IP for Debian 10 server on VirtualBox

[How do I setup a virtualbox server with a static ip?](https://superuser.com/questions/357120/how-do-i-setup-a-virtualbox-server-with-a-static-ip)

[What does "enp0s3" mean/stand for?](https://community.spiceworks.com/topic/975404-what-does-enp0s3-mean-stand-for)

[Why is my network interface named enp0s25 instead of eth0?](https://askubuntu.com/questions/704361/why-is-my-network-interface-named-enp0s25-instead-of-eth0)

[Virtualbox issue: how to use static ip in linux guest with windows host using dynamic ip?](https://superuser.com/questions/491003/virtualbox-issue-how-to-use-static-ip-in-linux-guest-with-windows-host-using-dy)

```Shell
apt-get install net-tools

netstat -nr
```

Gateway: 192.168.0.1

```Shell
ifdown
service networking restart
ifup
```