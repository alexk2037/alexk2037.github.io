---
title: "Configure Jigasi Properties"
date: 2021-09-22T00:03:39-04:00
draft: true
---

Open the jigasi properties file:

```
$ micro /etc/jitsi/jigasi/sip-communicator.properties
```

Comment out the SIP settings, as they are not needed for transcription:

{{< expand "/etc/jitsi/jigasi/sip-communicator.properties" >}}

```bash
# net.java.sip.communicator.impl.protocol.sip.
acc1403273890647=acc1403273890647
# net.java.sip.communicator.impl.protocol.sip.acc1403273890647.
ACCOUNT_UID=SIP\\:jigasisipuser@dev2dev.net
# net.java.sip.communicator.impl.protocol.sip.acc1403273890647.
PASSWORD=c2lwcHk6Y3VwMTIz
# net.java.sip.communicator.impl.protocol.sip.acc1403273890647.
PROTOCOL_NAME=SIP
# net.java.sip.communicator.impl.protocol.sip.acc1403273890647.
SERVER_ADDRESS=dev2dev.net
# net.java.sip.communicator.impl.protocol.sip.acc1403273890647.
USER_ID=jigasisipuser@dev2dev.net
...
# net.java.sip.communicator.impl.protocol.sip.acc1403273890647.
Encodings.red/90000=0
# net.java.sip.communicator.impl.protocol.sip.acc1jjj403273890647.
Encodings.speex/16000=0
# net.java.sip.communicator.impl.protocol.sip.acc1403273890647.
Encodings.speex/32000=0
# net.java.sip.communicator.impl.protocol.sip.acc1403273890647.
Encodings.speex/8000=0
# net.java.sip.communicator.impl.protocol.sip.acc1403273890647.
Encodings.telephone-event/8000=1
# net.java.sip.communicator.impl.protocol.sip.acc1403273890647.
Encodings.ulpfec/90000=0
# net.java.sip.communicator.impl.protocol.sip.acc1403273890647.
OVERRIDE_ENCODINGS=true
# net.java.sip.communicator.impl.protocol.sip.acc1403273890647.
DEFAULT_ENCRYPTION=false
Uncomment the  property:ALLOW_NON_SECURE
/etc/jitsi/jigasi/sip-communicator.properties
# To fix SSL/TLS required by client but not supported by server
org.jitsi.jigasi.xmpp.acc.ALLOW_NON_SECURE=true
Make sure to set the  property to true:USE_DEFAULT_STUN_SERVER
/etc/jitsi/jigasi/sip-communicator.properties
net.java.sip.communicator.impl.protocol.jabber.acc-xmpp-1.
USE_DEFAULT_STUN_SERVER=true
More on stun servers: Using Stun/Turn Server and why - Developers - Jitsi Community Forum - developers & users
Options Regarding Transcription
Enable transcription in the properties file. A sample is shown below:
/etc/jitsi/jigasi/sip-communicator.properties
# Options regarding Transcription. Read the README for a detailed 
description
# about each property
org.jitsi.jigasi.ENABLE_TRANSCRIPTION=true
org.jitsi.jigasi.ENABLE_SIP=false
# whether to use the more expensive, but better performing
# "video" model when doing transcription
org.jitsi.jigasi.transcription.USE_VIDEO_MODEL = false
# delivering final transcript
org.jitsi.jigasi.transcription.DIRECTORY=/var/lib/jigasi/transcripts
org.jitsi.jigasi.transcription.BASE_URL=http://localhost/
org.jitsi.jigasi.transcription.jetty.port=-1
org.jitsi.jigasi.transcription.ADVERTISE_URL=false
# save formats
org.jitsi.jigasi.transcription.SAVE_JSON=false
org.jitsi.jigasi.transcription.SAVE_TXT=true
# send formats
org.jitsi.jigasi.transcription.SEND_JSON=true
org.jitsi.jigasi.transcription.SEND_TXT=false
# Vosk server
org.jitsi.jigasi.transcription.customService=org.jitsi.jigasi.
transcription.VoskTranscriptionService
org.jitsi.jigasi.transcription.vosk.websocket_url=ws://localhost:2700
```
{{< /expand >}}

The vosk websocket can be set to a different server by replacing 'localhost' with the IP address of your Vosk Server. See the Vosk guides 
for more details on how to configure Vosk to listen at a different port.

There are more settings that need to be configured in the jigasi properties file, but we’ll come back to them later.