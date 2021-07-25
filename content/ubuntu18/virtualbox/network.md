---
title: "Network"
date: 2021-07-24T19:23:05-04:00
draft: true
---

## Ethernet

I am using an ethernet cable.

{{< expand "Virtualbox Network Setting Details" >}}

Adapter 1: Intel PRO/1000 MT Desktop (Bridged Adapter, Realteck PCIe GbE Family Controller #2)

{{< hint warning >}}
Don't use Intel (R) Dualband Wireless-AC 3168
{{< /hint >}}

{{< /expand >}}

## Configure static IP

[How to configure static ip address on Ubuntu (Linuxize)](https://linuxize.com/post/how-to-configure-static-ip-address-on-ubuntu-18-04/)

### Configure Static IP address on Ubuntu Server in VirtualBox

Current setup:

Windows 10 host connected to router by Ethernet cable

```Shell

windows\> netstat -nr

IPv4 Route Table
===========================================================================
Active Routes:
Network Destination        Netmask          Gateway       Interface  Metric
          0.0.0.0          0.0.0.0      192.168.0.1     192.168.0.13     25
...
```

I have a VirtualBox virtual machine running Ubuntu 18.04 Server.
The machine is called 'ubungu' and the main user is 'kapp'.

[DHCP reservation with Hitron router](https://business.shaw.ca/support/business-router-settings-dhcp-reservation)

**Client Name**: ubungu

**IP address**: 192.168.0.14

**MAC Address**: 08:00:27:af:4e:2f


Now I'm going to check that the reservation worked by restarting the VirtualBox 4 times and checking whether the ip address changes.
I'm expecting the IP address to remain as `192.168.0.14`.


| Restart | IP address   |
|:-------:|--------------|
| 1       | 192.168.0.14 |
| 2       | 192.168.0.14 |
| 3       | 192.168.0.14 |
| 4       | 192.168.0.14 |

Looks like it worked!

## Failed attempts and related resources

[Tutorial (Stack Exchange)](https://serverfault.com/questions/225155/virtualbox-how-to-set-up-networking-so-both-host-and-guest-can-access-internet)

A static IP will make development much easier.

[VirtualBox Static IP Tutorial](https://www.youtube.com/watch?v=haufmkuKq9A)

[VirtualBox Networking Docs](https://www.virtualbox.org/manual/ch06.html)

[Assign a static ip address virtualbox tutorial](https://www.youtube.com/watch?v=_LPXb-UlvkY)



