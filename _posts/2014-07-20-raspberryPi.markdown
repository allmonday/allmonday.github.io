---
layout: post
title:  "raspbery Pi"
categories: jekyll update
tags: 有空再玩
---

##Linux

###import direcotrie
directory | descriptions 
----- | -----
/etc/init.d| scripts to start up services
/proc| about running processes and OS
/usr/src| source of linux
/var| system logs and spool files
/var/cache| like apt-get store caches here

###chmod
```
chmod u+rwx, g-rwx,o-rwx file.txt
chmod 777 file.txt
```
###running headless
the ssh server on the Rapsberry Pi is enabled by default
run `raspi-config` if for some reason it doesn't work


##python in raspberry pi
### help
```python
help("topics")
help("modules")
```
###global
```python
n = 0

def setup():
	global n
	n = 100
```
###modules
modules |
-------|
RPi.GPIO|
Pygame|
SimpleCV|
Scipy|
Numpy|
Flask|
Requests|
PIL|
wxPython|
PySerial|
pyUSB|


to be continue.
