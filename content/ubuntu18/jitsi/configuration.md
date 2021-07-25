---
title: "Transcription Config"
date: 2021-07-23T23:33:58-04:00
draft: true
---

## Set up Jigasi

```Shell
# Install Jigasi
$ sudo apt install jigasi
```

SIP User: `jigasisipuser@freemeet.com`

Password: `abc123321bca`

Go through Jigasi properties file and uncomment all the transcription-related things. All the relevant settings are under the comment that says "# Options regarding Transcription". Additionally, we fix SSL/TLS required by client but not supported by server.

```Shell
$ vim /etc/jitsi/jigasi/sip-communicator.properties
```

{{< expand "Relevant Portions of Jigasi Properties File" >}}
```Shell
...
# To fix SSL/TLS required by client but not supported by server
org.jitsi.jigasi.xmpp.acc.ALLOW_NON_SECURE=true
...
# Options regarding Transcription. Read the README for a detailed description
# about each property

org.jitsi.jigasi.ENABLE_TRANSCRIPTION=true
org.jitsi.jigasi.ENABLE_SIP=false

# whether to use the more expensive, but better performing
# "video" model when doing transcription
org.jitsi.jigasi.transcription.USE_VIDEO_MODEL = false

# delivering final transcript
org.jitsi.jigasi.transcription.DIRECTORY=/var/lib/jigasi/transcripts
org.jitsi.jigasi.transcription.BASE_URL=http://freemeet.net/
org.jitsi.jigasi.transcription.jetty.port=-1
org.jitsi.jigasi.transcription.ADVERTISE_URL=false

# save formats
org.jitsi.jigasi.transcription.SAVE_JSON=false
org.jitsi.jigasi.transcription.SAVE_TXT=true

# send formats
org.jitsi.jigasi.transcription.SEND_JSON=true
org.jitsi.jigasi.transcription.SEND_TXT=false

# Vosk server
org.jitsi.jigasi.transcription.customService=org.jitsi.jigasi.transcription.VoskTranscriptionService
org.jitsi.jigasi.transcription.vosk.websocket_url=ws://freemeet.net:2700
```
{{< /expand >}}

Optionally, we can also comment out most of the SIP-related settings, since transcription doesn't require SIP.

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

# The entire SIP account block should be commented out
```

## Configure Transcription

### Enable Subtitle Button

By default, the 'Start Subtitles' button is missing. To fix this, first open the Jitsi React config.js:

```Shell
$ sudo -s
$ vim /etc/jitsi/meet/{{< param domain>}}-config.js
```

Uncomment `transcribingEnabled` and set to `true` and uncomment the `toolbarButtons` array. 

{{< expand "Relevant portions of config.js" >}}
```JS
...
    // Whether to enable live streaming or not.
    // liveStreamingEnabled: false,

    // Transcription (in interface_config,
    // subtitles and buttons can be configured)
    transcribingEnabled: true,

    // Enables automatic turning on captions when recording is started
    // autoCaptionOnRecord: false,
...
    // Moved from interfaceConfig(TOOLBAR_BUTTONS).
    // The name of the toolbar buttons to display in the toolbar, including the
    // "More actions" menu. If present, the button will display. Exceptions are
    // "livestreaming" and "recording" which also require being a moderator and
    // some other values in config.js to be enabled. Also, the "profile" button will
    // not display for users with a JWT.
    // Notes:
    // - it's impossible to choose which buttons go in the "More actions" menu
    // - it's impossible to control the placement of buttons
    // - 'desktop' controls the "Share your screen" button
    // - if `toolbarButtons` is undefined, we fallback to enabling all buttons on the UI
    toolbarButtons: [
       'microphone', 'camera', 'closedcaptions', 'desktop', 'embedmeeting', 'fullscreen',
       'fodeviceselection', 'hangup', 'profile', 'chat', 'recording',
       'livestreaming', 'etherpad', 'sharedvideo', 'shareaudio', 'settings', 'raisehand',
       'videoquality', 'filmstrip', 'invite', 'feedback', 'stats', 'shortcuts',
       'tileview', 'select-background', 'download', 'help', 'mute-everyone', 'mute-video-everyone', 'security'
    ],
...
```
{{< /expand >}}

Now start a meeting and check that the 'Start Subtitles' button is present.

### Configure Transcriber

**Prosody Config:** [Add a VirtualHost recorder](https://community.jitsi.org/t/configuration-of-jigasi/22095/7) to the prosody config file (above the Jicofo Components:):

```Shell
$ vim /etc/prosody/conf.d/{{< param domain >}}.cfg.lua
```

```lua
...
VirtualHost "recorder.{{< param domain >}}"
    modules_enabled = {
        "ping";
    }
    authentication = "internal_plain"

-- Proxy to jicofo's user JID, so that it doesn't have to register as a component.
Component "focus.freemeet.net" "client_proxy"
    target_address = "focus@auth.freemeet.net"
...
```

And uncomment https-ports (near the top of the file):

```lua
cross_domain_bosh = false;
consider_bosh_secure = true;
https_ports = { }; -- Remove this line to prevent listening on port 5284
```

[Add a transcriber user](https://www.notion.so/Jigasi-Setup-Jitsi-Configuration-Debian-10-dcc4713afafb444488fa7905a7303b72) using prosody, specifically to **recorder.{{< param domain >}}**:

```Shell
$ prosodyctl register transcriber recorder.{{< param domain >}} ai3eiSha2nae
```

Also, change the password of the auth user:

```Shell
$ prosodyctl passwd jigasi@auth.{{< param domain >}}

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

**Jigasi Config:** Add transcriber user to Jigasi Communicator Properties:

```Shell
$ vim /etc/jitsi/jigasi/sip-communicator.properties

# Inside file add:
org.jitsi.jigasi.xmpp.acc.USER_ID=transcriber@recorder.{{< param domain >}}
org.jitsi.jigasi.xmpp.acc.PASS=ai3eiSha2nae
org.jitsi.jigasi.xmpp.acc.ANONYMOUS_AUTH=false
```

**Jicofo Config:** Next, [add auth info](https://community.jitsi.org/t/configuration-of-jigasi/22095/7) to Jicofo Communicator Properties:

```Shell
$ vim /etc/jitsi/jicofo/sip-communicator.properties

# Inside file, append:
org.jitsi.jicofo.jigasi.BREWERY=JigasiBrewery@internal.auth.{{< param domain >}}
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

You may need to  [restart services](/ubuntu18/jitsi/logging) if the subtitles button still doesn't show up:

```Shell
# Restart in this order
$ systemctl restart nginx && systemctl restart prosody && systemctl restart jitsi-videobridge2 && systemctl restart jicofo && systemctl restart jigasi
```

### END RESULT

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/e00d5527-ae8c-43d4-9d8b-2330cb52f11a/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/e00d5527-ae8c-43d4-9d8b-2330cb52f11a/Untitled.png)

# Troubleshooting

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/63004cf4-0bad-4051-95b6-4e669df8d927/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/63004cf4-0bad-4051-95b6-4e669df8d927/Untitled.png)

**Delete prosody accounts and add them back again**

```Shell
# Delete users
$ prosodyctl deluser jigasi@auth.{{< param domain >}}
$ prosodyctl deluser transcriber@recorder.{{< param domain >}}

# Check folders to make sure they were deleted
$ ls /var/lib/prosody/recorder*/accounts/ # Should be empty
$ ls /var/lib/prosody/auth*/accounts/ # Should not return auth.dat

# Restart prosody
$ systemctl restart prosody
$ systemctl status prosody

# Add users again
$ prosodyctl register jigasi auth.{{< param domain >}} vixohv2geeNa
$ prosodyctl register transcriber recorder.{{< param domain >}} ai3eiSha2nae

# Check folders to make sure they were deleted
$ ls /var/lib/prosody/recorder*/accounts/ # Should return transcriber.dat
$ ls /var/lib/prosody/auth*/accounts/ # Should return jigasi.dat

# Restart prosody
$ systemctl restart prosody
$ systemctl status prosody
```
