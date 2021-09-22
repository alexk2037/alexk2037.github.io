

### Start, Stop, Restart Jitsi Components

```bash
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

### Follow All Logs

```bash
$ tail -f /var/log/jitsi/*.log
```

### Meet UI

*Logs*

Inspect browser console (<Ctrl-I> or <Cmd-I> for most browsers)

*Settings*

```bash
$ micro /etc/jitsi/meet/$(hostname -f)-config.js
```

### Jigasi

*Logs*

```bash
# Monitor Jigasi Logs
$ tail -f /var/log/jitsi/jigasi.log
# Ways to filter jigasi logs
$ tail -f /var/log/jitsi/jigasi.log | grep SEVERE
$ tail -f /var/log/jitsi/jigasi.log | egrep -i --color -A 5 
'severe|vosk'
$ egrep -i --color 'warn|severe|vosk' /var/log/jitsi/jigasi.log
$ egrep -i --color -A 5 'severe|vosk' /var/log/jitsi/jigasi.log
# Systemd Logs
$ egrep --color 'jigasi' /var/log/syslog
```

*Settings*

```bash
# Properties
$ micro /etc/jitsi/jigasi/sip-communicator.properties
# Config
JAVA_SYS_PROPS="-Djava.util.logging.config.file=/etc/jitsi/jigasi
/logging.properties"
```

### Vosk

*Logs*

```bash
# Check logs
$ journalctl --user -u vosk_service --since today
# Follow logs
$ journalctl --user -u vosk_service --since today -f
# Change Log Levels
$ micro ~/vosk-server/websocket/asr_server.py
```

*Update*

```bash
# Run after updating .service file
systemctl --user stop vosk_service;systemctl --user daemon-reload;
systemctl --user start vosk_service
```

*Settings*

```bash
# Environment variables
$ micro /etc/environment
# Server File
$ micro ~/vosk-server/websocket/asr_server.py
```


### JVB

*Logs*

*Settings*

```bash
# Properties
$ micro /etc/jitsi/videobridge/sip-communicator.properties
```

### Jicofo

*Logs*

```bash
# Monitor Jigasi Logs
$ tail -f /var/log/jitsi/jicofo.log
# Systemd Logs
$ egrep --color 'jicofo' /var/log/syslog
# Ways to filter jicofo logs
$ tail -f /var/log/jitsi/jicofo.log | grep SEVERE
$ tail -f /var/log/jitsi/jicofo.log | egrep -i --color -A 5 
'severe|vosk'
$ egrep -i --color 'warn|severe' /var/log/jitsi/jicofo.log
$ egrep -i --color -A 5 'severe' /var/log/jitsi/jicofo.log
```

*Settings*

```bash
# Properties
$ micro /etc/jitsi/jicofo/sip-communicator.properties
```

### Prosody

*Logs*

```bash
# Follow Prosody logs
$ tail -f /var/log/prosody/prosody.log
$ tail -f /var/log/prosody/prosody.err
```

*Settings*

```bash
# Settings related to Jitsi Meet
$ micro /etc/prosody/conf.d/$(hostname -f).cfg.lua
```

### Nginx

*Logs*

```bash
$ tail -f /var/log/nginx/access.log
$ tail -f /var/log/nginx/error.log
# Both at the same time
$ tail -f /var/log/nginx/*.log
```

### Settings

```bash
$ micro /etc/nginx/sites-available/$(hostname -f).conf
$ micro /etc/nginx/sites-available/default
```