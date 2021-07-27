---
title: "Python Setup"
date: 2021-07-23T23:14:39-04:00
draft: false
---

**Install Python 3.8**

{{< expand "What version of Python can I use?" >}}
By default, Ubuntu 18.04 comes with Python 3.6, however,for Vosk [nshmyrev on GitHub recommends Python 3.8](https://github.com/alphacep/vosk-api/issues/108#issuecomment-632835185)
{{< /expand >}}

Follow the article step by step:
[How to install Python 3.8 on Ubuntu 18.04 (Linuxize)](https://linuxize.com/post/how-to-install-python-3-8-on-ubuntu-18-04/)

To set Python 3.8.11 as the default simply add the following to the end of your Shell profile

```Shell
$ vim ~/.bashrc

# ~/.bashrc
alias python3="python3.8"

$ source ~/.bashrc

# Test
$ python3 -V
>>> Python 3.8.11
```

**Python Venv Setup**

Create a virtual environment:

```Shell
# Use Python 3.8.11
$ sudo apt install python3.8-venv
$ python3 -m venv venv
$ source venv/bin/activate

# You shell prompt should look like this now:
(venv) user@hostname:~$
```

