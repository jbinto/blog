---
layout: post
title: "Quickfix: Debian Squeeze - eth0 not coming up at boot"
date: 2012-10-27 10:39
comments: true
categories: 
---

**Problem:** After rebooting a remote Debian Squeeze server, the eth0 network
interface would not come up. ``/etc/network/interfaces`` was configured correctly.
The only way to get the network up was to run ``ifconfig eth0 up`` and
``dhclient eth0``.

**Configuration:** 

``` text /etc/network/interfaces 
# Used by ifup(8) and ifdown(8). See the interfaces(5) manpage or
# /usr/share/doc/ifupdown/examples for more information.

auto lo
iface lo inet loopback

auto eth0
iface eth0 inet static
address 1.2.3.4
netmask 255.255.255.0
gateway 1.2.3.1
``` 

**Solution:**

The ``ifupdown`` package wasn't installed for some reason (probably due to
upgrade issues).
``/etc/init.d/networking`` fails silently when ``ifup`` can't be found. 

``` bash
$ apt-get install ifupdown
$ /etc/init.d/networking restart
```
