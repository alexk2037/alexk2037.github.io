---
title: "Logging"
date: 2021-07-25T11:58:26-04:00
draft: false
---

### Start, Stop, Restart Jitsi Components

```Shell
# Status
$ systemctl status nginx prosody jitsi-videobridge2 jicofo jigasi

# Stop
$ systemctl stop nginx prosody jitsi-videobridge2 jicofo jigasi

# Clear all logs
$ rm /var/log/jitsi/*.log

# Start in this order
$ systemctl start nginx
$ systemctl start prosody 
$ systemctl start jitsi-videobridge2
$ systemctl start jicofo
$ systemctl start jigasi

# Restart in this order
$ systemctl restart nginx
$ systemctl restart prosody 
$ systemctl restart jitsi-videobridge2
$ systemctl restart jicofo
$ systemctl restart jigasi
```

## Logs & Settings

### All Logs

```Shell
$ tail -f /var/log/jitsi/*.log
```

### Jigasi

**Logs**

```Shell
# Systemd Logs
$ egrep --color 'jigasi' /var/log/syslog

# Monitor Jigasi Logs
$ tail -f /var/log/jitsi/jigasi.log

# Filter jigasi logs
$ tail -f /var/log/jitsi/jigasi.log | grep SEVERE
$ tail -f /var/log/jitsi/jigasi.log | egrep -i --color -A 5 'severe|vosk'

$ egrep -i --color 'warn|severe|vosk' /var/log/jitsi/jigasi.log
$ egrep -i --color -A 5 'severe|vosk' /var/log/jitsi/jigasi.log
```

**Settings**

```Shell
# Properties
$ vim /etc/jitsi/jigasi/sip-communicator.properties

# In case of file error
$ rm /etc/jitsi/jigasi/.sip-communicator.properties.swp

# Config
JAVA_SYS_PROPS="-Djava.util.logging.config.file=/etc/jitsi/jigasi/logging.properties"
```

### Vosk

```Shell
# Check logs
$ tail -f /var/log/syslog
$ tail -f /var/log/syslog | egrep --color 'python3'

$ grep 'Vosk Service' /var/log/syslog
$ grep 'python' /var/log/syslog
```

### JVB

**Logs**

**Settings**

```Shell
# Properties
$ vim /etc/jitsi/videobridge/sip-communicator.properties

# In case of file error
$ rm /etc/jitsi/videobridge/.sip-communicator.properties.swp
```

### Jicofo

**Logs**

```Shell
# Systemd Logs
$ egrep --color 'jicofo' /var/log/syslog

# Monitor Jigasi Logs
$ tail -f /var/log/jitsi/jicofo.log

# Filter jigasi logs
$ tail -f /var/log/jitsi/jicofo.log | grep SEVERE
$ tail -f /var/log/jitsi/jicofo.log | egrep -i --color -A 5 'severe|vosk'

$ egrep -i --color 'warn|severe|vosk' /var/log/jitsi/jicofo.log
$ egrep -i --color -A 5 'severe|vosk' /var/log/jitsi/jicofo.log
```

**Settings**

### Prosody

**Logs**

```Shell
$ tail -f /var/log/prosody/prosody.log
```

**Settings**

```Shell
$ vim /etc/prosody/conf.d/freemeet.net.cfg.lua
```