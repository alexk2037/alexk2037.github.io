---
title: "Network"
date: 2021-07-24T19:23:05-04:00
draft: true
weight: 10
---

## Ethernet

I have an ethernet cable

{{< expand "Virtualbox Network Setting Details" >}}

After turning your VM off, go to settings. Under Network configure the following:

Adapter 1: Bridged Adapter, Realteck PCIe GbE Family Controller #2

{{< hint warning >}}
Don't use Intel (R) Dualband Wireless-AC 3168 if you use Bridged Adapter with an ethernet cable
{{< /hint >}}

{{< /expand >}}

## Configure static IP

From the article [How to configure static ip address on Ubuntu (Linuxize)](https://linuxize.com/post/how-to-configure-static-ip-address-on-ubuntu-18-04/), read the paragraph under "Configuring Static IP address using DHCP"

Essentially you want to fiddle around with your router settings.

If you are on windows, open up a Command Prompt or PowerShell and type:

```Shell

> netstat -nr

# You might see some info displayed like so:
...
IPv4 Route Table
===========================================================================
Active Routes:
Network Destination        Netmask          Gateway       Interface  Metric
          0.0.0.0          0.0.0.0      192.168.0.1     192.168.0.13     25
...
```

Under `Ethernet Adapter 2` there should be a heading called 'Gateway'.
That's the IP address of your router. For example, mine was 192.168.0.1.
Copy that address and paste it into your web browser (like Chrome or Firefox). You should see a webpage from your router. Some router models or Internet Service Providers (ISPs) may require you to enter a username and password, but a quick google search of the 'router [model/ISP] password' should get you some helpful information. My router didn't even ask for a username and the password was 'admin'.

### Configure Static IP address on Ubuntu Server in VirtualBox

Turn on your VM if it isn't already on. Figure out its IP address by running.

```Shell
$ ip addr show
>>> {{< param ipAddress >}}
```

If you restart your VM a couple of times, chances are that your VM will have a different IP address each time. This is because your router assigns your VM a **dynamic IP address** (dynamic = moving). We are trying to set up a server, so instead we want a static IP address.

Go to your router and change your VM's IP address to static (i.e. reserve that address for your virtual machine).
For most routers, it should be fairly straightforward. However, if you are unsure how assign a static IP, then google [DHCP reservation with [your model] router.

Next, we install [Vosk](/ubuntu18/vosk/python)

## Failed attempts and related resources

I read/watched these resources before I learned that setting a static IP from your router is MUCH easier.

* [Tutorial (Stack Exchange)](https://serverfault.com/questions/225155/virtualbox-how-to-set-up-networking-so-both-host-and-guest-can-access-internet)

* [VirtualBox Static IP Tutorial](https://www.youtube.com/watch?v=haufmkuKq9A)

* [VirtualBox Networking Docs](https://www.virtualbox.org/manual/ch06.html)

* [Assign a static ip address virtualbox tutorial](https://www.youtube.com/watch?v=_LPXb-UlvkY)

