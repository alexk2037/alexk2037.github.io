---
title: "Configure Transcriber Authentication"
date: 2021-09-22T00:03:39-04:00
draft: false
---

### Prosody Config

I wrote this guide based on my setup, where the hostname of my Jitsi server was [http://alexjit.dev2dev.net](http://alexjit.dev2dev.net) . When you copy paste code, you can replace 'alexjit.dev2dev.net' with your own hostname


$(hostname -f) will evaluate to your hostname




**Add VirtualHost**

Add a VirtualHost recorder to the prosody config file:

```bash
$ micro /etc/prosody/conf.d/$(hostname -f).cfg.lua
```

Inside config file, append a Virtual Host (See this [Jitsi community thread](https://community.jitsi.org/t/configuration-of-jigasi/22095/23) for more details)

{{< expand "/etc/prosody/conf.d/alexjit.dev2dev.net.cfg.lua" >}}
The ‘internal.auth’ component should already be present in the config file. I left it in the code block below for context.

```lua
...
Component "internal.auth.alexjit.dev2dev.net" "muc"
    modules_enabled = {
      "ping";
    }
    storage = "null"
    muc_room_cache_size = 1000
VirtualHost "recorder.alexjit.dev2dev.net"
    modules_enabled = {
      "ping";
    }
    authentication = "internal_plain"
...
```
{{< /expand >}}


Next, uncomment https-ports (near the top of the file). Optionally, you can add an interfaces property:

{{< expand "/etc/prosody/conf.d/alexjit.dev2dev.net.cfg.lua" >}}
```lua
cross_domain_bosh = false;
consider_bosh_secure = true;
https_ports = { }; -- Remove this line to prevent listening on port 5284
interfaces = { "*" };
...
```
The `"*"` in `interfaces = { "*"}` tells prosody to listen to all interfaces. See [prosody docs](https://prosody.im/doc/ports) for more details.
{{< /expand >}}


### Add Users

Add a transcriber user using prosody, specifically to `recorder.alexjit.dev2dev.net` (or more generally, `recorder.yourdomainname.whatever`)

```bash
$ prosodyctl register transcriber recorder.alexjit.dev2dev.net ai3eiSha2nae
```

Also, change the password of the auth user:

```bash
$ prosodyctl passwd jigasi@auth.alexjit.dev2dev.net
```
This will prompt you to enter a new password (my password was `vixohv2geeNa`). If you want to see the prosody accounts go to:
`$ cd /var/lib/prosody/`
, then cd into the relevant directory.

Convert the password you just entered (my password was `vixohv2geeNa`) to base64. The easiest way is to use an [online base64 encoder](https://www.base64encode.org/), but you could also use Python:

```python
>>> import base64
>>> base64.b64encode(b'vixohv2geeNa')
b'dml4b2h2MmdlZU5h'
>>> exit()
```

### Jigasi Config

Put the base64 password in the Jigasi Properties file:

{{< expand "/etc/jitsi/jigasi/sip-communicator.properties" >}}
```shell
...
net.java.sip.communicator.impl.protocol.jabber.acc-xmpp-1.PASSWORD=dml4b2h2MmdlZU5h
...
```
{{< /expand >}}

Then add the transcriber user. The relevant properties are commented by default, so uncomment them and change the values:

{{< expand "/etc/jitsi/jigasi/sip-communicator.properties" >}}
```bash
...
org.jitsi.jigasi.xmpp.acc.USER_ID=transcriber@recorder.alexjit.dev2dev.net
org.jitsi.jigasi.xmpp.acc.PASS=ai3eiSha2nae
org.jitsi.jigasi.xmpp.acc.ANONYMOUS_AUTH=false
...
```
{{< /expand >}}

### Jicofo Config

Next, [append authentication info](https://community.jitsi.org/t/configuration-of-jigasi/22095/7) to the Jicofo Properties:

{{< expand "/etc/jitsi/jicofo/sip-communicator.properties" >}}
```bash
org.jitsi.jicofo.jigasi.BREWERY=JigasiBrewery@internal.auth.alexjit.dev2dev.net
```
{{< /expand >}}

### Next Steps

This is the end of this section.