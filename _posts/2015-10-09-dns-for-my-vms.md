---
title: DNS for My VMs
---

Want to use domain names to access your virtual machines instead of memorizing IP addresses?
Try adding the line `nameserve 192.168.122.1` to your `/etc/resolv.conf` file before your other name servers.
Of course, this assumes that `192.168.122.1` is the IP address of your host machine on your virtual network.
Also, this assumes that you're using [libvirt](https://libvirt.org) and [dnsmasq](http://www.thekelleys.org.uk/dnsmasq/doc.html) which is the default if you're using Fedora or Red Hat/CentOS.

Now you can access your virtual machines by hostname!
Well, kind of.
You have to add a `.` to the end of your hostname when you're using it.
So, if for example, your hostname was `web-devel`, you'd have to connect to `web-devel.`.
Weird, but that period at the end actually makes it a fully qualified domain name.
This a DNS thing and is more complicated than I want to get into here, but it does work as a quick hack!


