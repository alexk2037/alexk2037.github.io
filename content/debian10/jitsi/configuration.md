---
title: "Configuration"
date: 2021-07-23T23:33:58-04:00
draft: false
---

### Set up Jigasi

```Shell
# Install Jigasi
$ sudo apt install jigasi
```

Inteded SIP User: `jigasisipuser@sipprovider.com`

Inteded Password: `abc123321bca`

Go through Jigasi properties file and uncomment all the transcription-related things. All the settings are under the comment that says "# Options regarding Transcription":

```Shell
$ vim /etc/jitsi/jigasi/sip-communicator.properties
```

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/8ac68d55-9511-42ea-8838-6a4a3b5bec32/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/8ac68d55-9511-42ea-8838-6a4a3b5bec32/Untitled.png)

```Shell
# Optional: Comment out SIP properties in Jigasi config

$ vim /etc/jitsi/jigasi/sip-communicator.properties

# Align cursor at start of this line:
net.java.sip.communicator.impl.protocol.sip.acc1403273890647=acc1403273890647

# Using vim in Windows Terminal, perform
# <Ctrl-Alt-V> to enter Visual Block mode
# <Ctrl-]> to highlight entire block
# <Ctrl-I> to enter multiline insert mode
# press '# ' on keyboard
# <Esc>

# et Voila! the entire SIP account block should be commented out
```

### Enable Transcription

**Configure Subtitle Button**

When I started a meeting, the subtitle button was missing:

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/d730ca3e-61c5-4209-9f3f-1638b6754a1e/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/d730ca3e-61c5-4209-9f3f-1638b6754a1e/Untitled.png)

To fix this, I opened my JMS config.js in vim:

```Shell
$ sudo -s
$ vim /etc/jitsi/meet/debbie.net-config.js
```

I uncommented `transcribingEnabled` and set to `true` and uncommented the `toolbarButtons` array. You may need to  restart services if the button still doesn't show up:

```Shell
# Restart in this order
$ systemctl restart nginx && systemctl restart prosody && systemctl restart jitsi-videobridge2 && systemctl restart jicofo && systemctl restart jigasi
```

### **Configure Transcriber**

**Prosody Config:** Finally, [I added a VirtualHost recorder](https://community.jitsi.org/t/configuration-of-jigasi/22095/7) to the prosody config file, which was $(hostname -f).cfg.lua

```Shell
$ vim /etc/prosody/conf.d/debbie.net.cfg.lua
```

Inside the debbie.net.cfg.lua file, add the following code above the conference Component:

```lua
VirtualHost "recorder.debbie.net"
    modules_enabled = {
        "ping";
    }
    authentication = "internal_plain"
```

[Add a transcriber user](https://www.notion.so/Jigasi-Setup-Jitsi-Configuration-Debian-10-dcc4713afafb444488fa7905a7303b72) using prosody, specifically to **recorder.debbie.net**:

```Shell
$ prosodyctl register transcriber recorder.debbie.net ai3eiSha2nae
```

Also, change the password of the auth user:

```Shell
$ prosodyctl passwd jigasi@auth.debbie.net

# This will prompt you to enter a new password. If you want to see the prosody accounts go to:
$ cd /var/lib/prosody/

# Then cd into the relevant directory.
```

Convert that password (I used `vixohv2geeNa`) to base64 and put it in the Jigasi Properties file

```Shell
$ python3
>>> import base64
>>> base64.b64encode(b'vixohv2geeNa')
b'dml4b2h2MmdlZU5h'
>>> exit()

$ vim /etc/jitsi/jigasi/sip-communicator.properties

# Add password to xmpp password:
net.java.sip.communicator.impl.protocol.jabber.acc-xmpp-1.PASSWORD=dml4b2h2MmdlZU5h
```

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/f53bfc86-1ffa-445a-a6ee-c15143c24711/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/f53bfc86-1ffa-445a-a6ee-c15143c24711/Untitled.png)

**Jigasi Config:** Then I added the transcriber user to Jigasi Communicator Properties:

```Shell
$ vim /etc/jitsi/jigasi/sip-communicator.properties

# Inside file add:
org.jitsi.jigasi.xmpp.acc.USER_ID=transcriber@recorder.debbie.net
org.jitsi.jigasi.xmpp.acc.PASS=ai3eiSha2nae
org.jitsi.jigasi.xmpp.acc.ANONYMOUS_AUTH=false

org.jitsi.jigasi.xmpp.acc.ALLOW_NON_SECURE=true
```

**Jicofo Config:** Next, I [added auth info](https://community.jitsi.org/t/configuration-of-jigasi/22095/7) to Jicofo Communicator Properties:

```Shell
$ vim /etc/jitsi/jicofo/sip-communicator.properties

# Inside file, append:
org.jitsi.jicofo.jigasi.BREWERY=JigasiBrewery@internal.auth.debbie.net
```

Then restart JMS:

```Shell
# Restart in this order
$ systemctl stop nginx prosody jitsi-videobridge2 jicofo jigasi
$ systemctl status prosody jitsi-videobridge2 jicofo jigasi
$ systemctl start nginx prosody jitsi-videobridge2 jicofo jigasi

# Or
$ systemctl restart nginx prosody jitsi-videobridge2 jicofo jigasi
```

### END RESULT

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/e00d5527-ae8c-43d4-9d8b-2330cb52f11a/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/e00d5527-ae8c-43d4-9d8b-2330cb52f11a/Untitled.png)

# Troubleshooting

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/63004cf4-0bad-4051-95b6-4e669df8d927/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/63004cf4-0bad-4051-95b6-4e669df8d927/Untitled.png)

**Delete prosody accounts and add them back again**

```Shell
# Delete users
$ prosodyctl deluser jigasi@auth.{{< param ipAddress >}}
$ prosodyctl deluser transcriber@recorder.{{< param ipAddress >}}

# Check folders to make sure they were deleted
$ ls /var/lib/prosody/recorder*/accounts/ # Should return nothing
$ ls /var/lib/prosody/auth*/accounts/ # Should no auth

# Restart prosody
$ systemctl restart prosody
$ systemctl status prosody

# Add users again
$ prosodyctl register jigasi auth.{{< param ipAddress >}} vixohv2geeNa
$ prosodyctl register transcriber recorder.{{< param ipAddress >}} ai3eiSha2nae

# Check folders to make sure they were deleted
$ ls /var/lib/prosody/recorder*/accounts/ # Should return transcriber.dat
$ ls /var/lib/prosody/auth*/accounts/ # Should return jigasi.dat

# Restart prosody
$ systemctl restart prosody
$ systemctl status prosody
```
