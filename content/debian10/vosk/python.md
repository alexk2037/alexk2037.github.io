---
title: "General Python Setup"
date: 2021-07-23T23:14:39-04:00
draft: true
---

**Install Python 3.8 (RECOMMENDED ≥3.8 FOR VOSK)**

Follow the article step by step:
[How to install Python 3.8 on Debian 10 (Linuxize)](https://linuxize.com/post/how-to-install-python-3-8-on-debian-10/)

To set Python 3.8.2 as the default simply add the following to the end of your Shell profile

```Shell
# ~/.Shellrc
alias python3="python3.8"
```

**Python Venv Setup**

Create a virtual environment:

```Shell
# Use Python 3.8.2
$ python3 -m venv venv
$ source venv/bin/activate
```

