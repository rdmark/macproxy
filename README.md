Macproxy
========

A simple HTTP proxy script for putting early computers on the Web. Despite its name, there is nothing Mac specific about this proxy. It was originally designed with compatibility with the MacWeb web browser in mind, but has been tested on a variety of vintage web browsers since.

The proxy.py script runs a Flask server that takes all requests and proxies them, using html_utils.py to strip tags that are incompatible with, or pulls in contents that aren't parsable by old browsers such as Netscape 4 or MacWeb.

The proxy server listens to port 5000 by default, but the port number can be changed using a command line parameter.

Requirements
============

Python3 for running the script, venv if you want to use the virtual environment, or pip if you want to install libraries manually.

```
sudo apt install python3 python3-venv python3-pip
```

Usage
=====

The start_macproxy.sh shell script will create and manage a venv Python environment, and if successful launch the proxy script.

```
./start_macproxy.sh
```

Launch with a specific port number (defaults to port 5000):

```
./start_macproxy.sh --port=5001
```

You may also start the Python script by itself, using system Python.

```
pip3 install -r requirements.txt
python3 proxy.py
```

Launch with a specific port number:

```
python3 proxy.py --port 5001
```

Docker
======

With the bundled Dockerfile, you can run Macproxy in a container through the power of Docker Engine.

Either build an image yourself with `docker build`, or pull the [latest image from Docker Hub](https://hub.docker.com/r/rdmark/macproxy):

```
docker pull rdmark/macproxy:latest
```

Then launch the container with `docker run` (see below) or `docker compose up`. The examples assume the default port `5000`.

```
docker run --rm -p 5000:5000 macproxy
```

The below advanced options can be passed to the container through the `PROXY_ARGS` environment variable.

Advanced Options
================

Use the advanced options to change how Macproxy presents itself to the web, and how it processes the data it gets back.

By default, Macproxy will forward the actual User-Agent string of the originating browser in its request headers.
This option overrides this with an arbitrary string, allowing you to spoof as any browser. For instance, Opera Mini 8.0 for J2ME:

```
python3 proxy.py --user-agent "Opera/9.80 (J2ME/MIDP; Opera Mini/8.0.35158/36.2534; U; en) Presto/2.12.423 Version/12.16"
```

Selects the BeatifulSoup html formatter that Macproxy will use, e.g. the minimal formatter:
```
python3 proxy.py --html-formatter minimal
```

Turns off the conversion of select typographic symbols to ASCII characters:
```
python3 proxy.py --disable-char-conversion
```

Refer to Macproxy's helptext for more details:
```
python3 proxy.py -h
```

systemd service
===============

This repo comes with a systemd service configuration template, for controlling Macproxy on systems that use the systemd init system.
Edit the macproxy.service file and point the ExecStart= parameter to the location of the start_macproxy.sh file, e.g. from a git repo checkout in your home dir:

```
ExecStart=/home/myuser/macproxy/start_macproxy.sh
```

Then copy the service file to /etc/systemd/system and enable the service:

```
sudo cp macproxy.service /usr/etc/systemd/system/
sudo systemctl enable macproxy
sudo systemctl daemon-reload
sudo systemctl start macproxy
```
