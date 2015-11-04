---
title: Virtual Machine Consoles
---

Sometimes you don't want to open up a GUI to manage a virtual machine.
Sometimes you don't even have the luxury of a graphical display. The other day
I ran into a problem where the virtual machine was hosted on a remote host and
I couldn't figure out it's IP address. I've read articles explaining that you
should just use `arp` to get the IP/MAC address relation, but the VM connects
to the network via a bridge and apparently this doesn't work. My connection to
the remote machine was over a slow link, so viewing the desktop would have been
painful.

Luckily, libvirt's `virsh` command allows you to connect to the serial port of
your virtual machine. Unfortunately, my VM doesn't use the serial port by
default. Here, however, are the steps needed to have your linux VM connect to
the serial port. I've tested this with CentOS 6, but this should work with
other linux distributions.

### Linux Console

Add `console=tty0 console=ttyS0` to the kernel command line arguments of
`/boot/grub/grub.conf`. This tells the kernel to use `tty0` (your monitor) and
`ttyS0` (the serial port) for it's console. Now, when you boot up your machine,
you can use `virsh console` to connect to the virtual machine. That's it.
Pretty simple.

That's great and all, but now wouldn't it be nice to also view the grub console
to choose which kernel to boot into? We can do that too.

### GRUB Console

Edit `/boot/grub/grub.conf` and add the following lines.

```
serial --unit=0
terminal --timeout=5 serial console
```

This tells GRUB that the serial port to use is ttyS0 (unit 0) and for the
terminal, use either the serial port or the console. GRUB only shows up on one
of the terminal types. Either the serial port or the monitor (console) but not
both. The timeout option specifies how long to wait before automatically
choosing one. Without the timeout, it will just keep waiting for input on where
to set the terminal. But that's it!

Now I can work with my virtual machines even more on the command line.

