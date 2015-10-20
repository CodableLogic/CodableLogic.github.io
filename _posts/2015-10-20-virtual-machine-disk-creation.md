---
title: Virtual Machine Disk Creation
---

This is the command to build a simple qcow2 virtual machine disk that grows dynamically.
I like to use this on my laptop so that it doesn't chew through hard disk space.

> qemu-img create -f qcow2 _filename_ _size_

For example, `qemu-img create -f qcow2 centos7.qcow2 40G` will create a 40G virtual disk for my CentOS VM.
On disk, it's actually only 193KB to start out.
Eventually, this size will grow, but it wont take up the full 40G until 
I took this right from the man pages for `qemu-img` but I'm posting it for reference for myself.

