---
title: "Jitsi Setup"
date: 2021-07-23T23:11:41-04:00
---

## **Required packages and repository updates**

Install required packages

```Shell
# Data encryption
$ sudo apt install gnupg2

# Web server
$ sudo apt install nginx-full

# Java Runtime Environment
$ sudo apt install default-jre

# Java 11 (should already be installed on Debian 10)
$ sudo apt install openjdk-11-jre-headless
```

{{< expand "What is the recommended Java Version?" >}}
Jitsi team made optimizations for Java 8, however, Debian 10 (Buster) supports only Java 11 officially

{{< button href="https://community.jitsi.org/t/what-is-the-recommend-java-version/28955/2?" >}}Source: Jitsi Community Forum{{< /button >}}

{{< /expand >}}

Retrieve latest packages:

```Shell
$ sudo apt update

# Ensure support for apt repositories served via HTTPS
$ sudo apt install apt-transport-https

# Retrieve the latest package versions across all repositories
$ sudo apt update

$ sudo apt upgrade
```

{{< expand "Network Setup">}}

**Domain**: debbie.net

Open windows hosts file (C:\Windows\System32\drivers\etc\hosts) in notepad and add:

```Shell
# hosts
{{< param ipAddress >}} debbie.net
```

Then go to hosts file in Debian server:

```Shell
# /etc/hosts
{{< param ipAddress >}} debserver10.debbie.net debserver10
```




- Set up the Fully Qualified Domain Name (FQDN) (optional):

```Shell
$ nvim /etc/hosts

# Add to file:

127.0.0.1 localhost
{{< param ipAddress >}} debbie.net
```

{{< /expand >}}

### **Add the Jitsi package repository**

This will add the jitsi repository to your package sources to make the Jitsi Meet packages available.

```Shell
$ curl https://download.jitsi.org/jitsi-key.gpg.key | sudo sh -c 'gpg --dearmor > /usr/share/keyrings/jitsi-keyring.gpg'
$ echo 'deb [signed-by=/usr/share/keyrings/jitsi-keyring.gpg] https://download.jitsi.org stable/' | sudo tee /etc/apt/sources.list.d/jitsi-stable.list > /dev/null

# update all package sources
$ sudo apt update
```

### Setup and configure your firewall

```Shell
$ sudo apt install ufw
$ sudo ufw allow 80/tcp && sudo ufw allow 443/tcp && sudo ufw allow 10000/udp && sudo ufw allow 22/tcp && sudo ufw allow 3478/udp && sudo ufw allow 5349/tcp
$ sudo ufw enable
$ sudo ufw status verbose
```

### Install Jitsi Meet

**Hostname:** IP address of machine: {{< param ipAddress >}}

**SSL/TLS certificate generation:** Choose option (1) Generate TLS certificate

```Shell
# jitsi-meet installation
$ sudo apt install jitsi-meet
# Generate a Let's Encrypt certificate (optional, recommended)
$ sudo /usr/share/jitsi-meet/scripts/install-letsencrypt-cert.sh
```

> Note from Let's Encrypt: "Requested name {{< param ipAddress >}} is an IP address. The Let's Encrypt certificate authority will not issue certificates for a bare IP address.

### Systemd Commands

```Shell
# To reload the systemd changes on a running system execute 
$ sudo systemctl daemon-reload
$ sudo systemctl restart jitsi-videobridge2

# To check the tasks part execute 
$ sudo systemctl status jitsi-videobridge2
# and you should see Tasks: XX (limit: 65000).

# To check the files and process part execute 
$ cat /proc/`cat /var/run/jitsi-videobridge/jitsi-videobridge.pid`/limits
# and you should see:
Max processes             65000                65000                processes
Max open files            65000                65000                files
```

## End Result

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/6ce154f5-0cbd-4f91-84a0-768b5b488ba0/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/6ce154f5-0cbd-4f91-84a0-768b5b488ba0/Untitled.png)

Notice that the HTTPS is crossed out because the Let's Encrypt certificate failed to generate