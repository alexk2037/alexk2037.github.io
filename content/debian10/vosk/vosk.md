---
title: "Vosk Setup"
date: 2021-07-23T23:03:33-04:00
---

**Git & Vosk Server files**

```Shell
$ git clone https://github.com/alphacep/vosk-server.git
```

**Install dependencies**

```Shell
$ python3 -m pip install aiortc aiohttp aiorpc vosk 
$ python3 -m pip install websockets
```

**Download Speech Recognition model:**

Models can be found here: [VOSK Models (alphacephei.com)](https://alphacephei.com/vosk/models)

```Shell
# /home/$USER
$ wget http://alphacephei.com/vosk/models/vosk-model-small-en-us-0.15.zip
# $ sudo apt-get install unzip
$ unzip vosk-model-small-en-us-0.15.zip
# Rename the downloaded model for Vosk API to work
$ mv vosk-model-small-en-us-0.15 model
```

**Create .service File**

```Shell
$ mkdir -p ~/.config/systemd/user
$ cd ~/.config/systemd/user
$ touch vosk_service.service
# Check
$ systemctl --user list-unit-files | grep vosk_service
>>> vosk_service.service           disabled
# Open service file
$ nvim ~/.config/systemd/user/vosk_service.service
```

**Write to .service File**

Open .service file with your text editor then paste the following. Make sure to replace `kapp` with your username instead. If you are unsure about your username, type `$ echo $USER`

```Shell
# systemd unit file for the Vosk Service

[Unit]
# Human readable name of the unit
Description=Vosk Service

[Service]
# Command to execute when the service is started
WorkingDirectory=/home/kapp
ExecStart=/home/kapp/venv/bin/python3.8 /home/kapp/vosk-server/websocket/asr_server.py

# Disable Python's buffering of STDOUT and STDERR, so that output from the
# service shows up immediately in systemd's logs
Environment=PYTHONUNBUFFERED=1

# Automatically restart the service if it crashes
Restart=on-failure

# Our service will notify systemd once it is up and running
Type=simple

# Use a dedicated user to run our service (only for system services)
# User=python_demo_service

[Install]
# Tell systemd to automatically start this service when the system boots
# (assuming the service is enabled)
WantedBy=default.target
```

**Activate service**

```Shell
$ systemctl --user daemon-reload
$ systemctl --user status vosk_service
$ systemctl --user start vosk_service
$ systemctl --user status vosk_service
>>> # Vosk Service should be green and running now!
# To exit status log, press 'q' (for quit) on your keyboard

# Check logs
$ sudo -s
$ grep 'Vosk Service' /var/log/syslog
$ grep 'python' /var/log/syslog
```

**Enable Vosk to start on boot up:**

```Shell
$ systemctl --user enable vosk_service
```