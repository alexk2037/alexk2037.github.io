---
title: "Vosk Setup"
date: 2021-07-23T23:03:33-04:00
---

## Relevant Links

* [Official Website](https://alphacephei.com/vosk/)
* [Vosk API](https://github.com/alphacep/vosk-api)
* [Vosk Server](https://github.com/alphacep/vosk-server)

## Set up Vosk as a system daemon

We'll set up Vosk as a [daemon](https://en.wikipedia.org/wiki/Daemon_(computing)) using [systemd](https://systemd.io/). In other words, we'll configure Vosk to run on the VM in the background.

**Git & Vosk Server files**

```Shell
$ cd $HOME
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
$ touch ~/.config/systemd/user/vosk_service.service
# Check
$ systemctl --user list-unit-files | grep vosk_service
>>> vosk_service.service           masked

```

**Write to .service File**

Open .service file with your text editor,

```Shell
# Open service file
$ vim ~/.config/systemd/user/vosk_service.service
```

then paste the following text. Make sure to replace `kapp` with your username instead. If you are unsure about your username, enter `$ echo $USER` into your shell.

```Shell
# systemd unit file for the Vosk Service

[Unit]
# Human readable name of the unit
Description=Vosk Service

[Service]
# Command to execute when the service is started
WorkingDirectory=/home/kapp
ExecStart=/home/kapp/venv/bin/python3 /home/kapp/vosk-server/websocket/asr_server.py

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
# Press 'q' (for quit) to see your shell prompt again

$ systemctl --user start vosk_service
$ systemctl --user status vosk_service
>>> # Vosk Service should be green and running now!
# To exit status log, press 'q' (for quit) on your keyboard

# Check logs
$ sudo -s
$ grep 'Vosk Service' /var/log/syslog
$ grep 'python' /var/log/syslog
$ exit
```

**Enable Vosk to start on boot up:**

```Shell
$ systemctl --user enable vosk_service
```

## (Optional) Configure Vosk for Jitsi on a Separate Computer

This is only necessary if you have Jitsi and Vosk running on separate servers.

Jitsi uses port 10000 for audio and video communication, so we'll change the Vosk listening port [as suggested by one of the main developers of vosk](https://github.com/alphacep/vosk-api/issues/113#issuecomment-884750079)

```Shell
# Stop vosk daemon
$ systemctl --user stop vosk_service
$ systemctl --user status vosk_service
```

Create environment variable

```Shell
$ sudo -s
$ vim /etc/environment

# append to /etc/environment
VOSK_SERVER_PORT=10000
```

You may need to open a new shell. Next, make sure the environment variable was set correctly

```Shell
# test
$ printenv VOSK_SERVER_PORT
>>> 10000

# alternative test
$ python3
>>> import os
>>> os.environ.get('VOSK_SERVER_PORT', 2700)
'10000'
```

Start the Vosk service again:

```Shell
$ systemctl --user daemon-reload
$ systemctl --user start vosk_service
$ systemctl --user status vosk_service
```

## Other Things

Watch Vosk logs

```Shell
$ tail -f /var/log/syslog
```

## Further Reading

* For more info on python daemons see [https://github.com/torfsen/python-systemd-tutorial](https://github.com/torfsen/python-systemd-tutorial) 
* For more on Vosk server see [https://github.com/alphacep/vosk-server](https://github.com/alphacep/vosk-server)

## Failed Attempts and Relevant Resources

Initially I tried setting up the daemon [using python only](https://pypi.org/project/python-daemon/).
[This tutorial](https://dpbl.wordpress.com/2017/02/12/a-tutorial-on-python-daemon/) was helpful for understanding the python-daemon library, but one of the comments for that article was especially helpful because they pointed out that most people these days use systemd to manage daemons. That pointed me in the right direction to figure out all the steps above.