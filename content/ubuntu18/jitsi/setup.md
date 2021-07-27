---
title: "Jitsi Setup"
date: 2021-07-23T23:11:41-04:00
draft: true
---

[Jitsi Self-Hosting Guide](https://jitsi.github.io/handbook/docs/devops-guide/devops-guide-quickstart)

## Install required packages and update repository

{{< expand "Install packages" >}}

```Shell
# For data encryption
$ sudo apt install gnupg2

# Web server
$ sudo apt install nginx-full
# TEST: enter you ip address in your browser. You should see a default Nginx landing page

# Install Java Runtime Environment
$ sudo apt install default-jre
$ java -version
>>> openjdk version "11.0.11" 2021-04-20
OpenJDK Runtime Environment (build 11.0.11+9-Ubuntu-0ubuntu2.18.04)
OpenJDK 64-Bit Server VM (build 11.0.11+9-Ubuntu-0ubuntu2.18.04, mixed mode, sharing)
```

{{< expand "What is the recommended Java Version?" >}}
Jitsi team made optimizations for Java 8, however, development is moving towards Java 11 (particularly on Debian systems)

{{< button href="https://community.jitsi.org/t/what-is-the-recommend-java-version/28955/2?" >}}Source: Jitsi Community Forum{{< /button >}}

{{< /expand >}}

Retrieve latest packages:

```Shell
$ sudo apt update

# Ensure support for apt repositories served via HTTPS
$ sudo apt install apt-transport-https
$ sudo apt upgrade

# On Ubuntu systems, Jitsi requires dependencies from Ubuntu's universe package repo
$ sudo apt-add-repository universe

# Retrieve the latest package versions across all repositories
$ sudo apt update
```

## Network Configuration


### Set up the Fully Qualified Domain Name (FQDN) (optional):

```Shell
$ sudo hostnamectl set-hostname {{< param domain >}}
$ vim /etc/hosts

# /etc/hosts
127.0.0.1 localhost
{{< param ipAddress >}} {{< param domain >}}
```

Then test to see if everything is working

```Shell
$ ping "$(hostname)"
>>> PING freemeet.net ({{< param ipAddress >}}) 56(84) bytes of data.
64 bytes from freemeet.net ({{< param ipAddress >}}): icmp_seq=1 ttl=64 time=0.019 ms
64 bytes from freemeet.net ({{< param ipAddress >}}): icmp_seq=2 ttl=64 time=0.031 ms
64 bytes from freemeet.net ({{< param ipAddress >}}): icmp_seq=3 ttl=64 time=0.028 ms
^C
--- freemeet.net ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2032ms
rtt min/avg/max/mdev = 0.019/0.026/0.031/0.005 ms
```

{{< /expand >}}

## Install Jitsi

### Add Jitsi repositories

This will add the jitsi repository to your package sources to make the Jitsi Meet packages available.

```Shell
$ curl https://download.jitsi.org/jitsi-key.gpg.key | sudo sh -c 'gpg --dearmor > /usr/share/keyrings/jitsi-keyring.gpg'
$ echo 'deb [signed-by=/usr/share/keyrings/jitsi-keyring.gpg] https://download.jitsi.org stable/' | sudo tee /etc/apt/sources.list.d/jitsi-stable.list > /dev/null

# update all package sources
$ sudo apt update
```

### Setup and configure your firewall

```Shell
$ sudo ufw allow 80/tcp && sudo ufw allow 443/tcp && sudo ufw allow 10000/udp && sudo ufw allow 22/tcp && sudo ufw allow 3478/udp && sudo ufw allow 5349/tcp
$ sudo ufw enable
$ sudo ufw status verbose
```

### Install Jitsi Meet

**Hostname:** {{< param domain >}}

**SSL/TLS certificate generation:** Choose option (1) Generate TLS certificate

{{< hint danger >}}
Don't run Let's Encrypt script after installing Jitsi
```Shell
# DON'T run this
$ sudo /usr/share/jitsi-meet/scripts/install-letsencrypt-cert.sh
```
{{< /hint >}}

```Shell
# jitsi-meet installation
$ sudo apt install jitsi-meet
```

Test the installation by going to {{< param domain >}} in your browser.

Because we used a self-signed certificate, there will be a warning from your browser.
Clicked 'Advanced' and continue to {{< param domain >}}. 
It's not actually unsafe because we are the only clients visiting the website.

Notice that the HTTPS is crossed out because the Let's Encrypt certificate failed to generate.

## Next Steps

Read [Jitsi Configuration docs](/ubuntu18/jitsi/configuration)