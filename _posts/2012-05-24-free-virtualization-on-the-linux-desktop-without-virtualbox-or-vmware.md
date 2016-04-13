---
layout: post
published: true
title: Free virtualization on the Linux desktop without VirtualBox or VMware
date: 2012-05-24 18:00:00
categories:
- web-development
tags:
- linux
- tutorial
- ubuntu

---

For a number of years I have ran virtual machines on Linux for testing purposes and to run software that demanded a Windows environment. I started with VirtualBox back in the day and never bothered to switch. Until now. I&#39;ve been working with Proxmox (a free and open source hypervisor that makes use of KVM) a lot and decided to try something besides VirtualBox for my new laptop. Here&#39;s how you can set it up.

---


I am running Ubuntu 12.04 on my laptop (from here on I'll refer to it as "host"). Virtualization is done via KVM and the gui management is done with Virtual Machine Manager. When finished you will be able to create, run, manage, and delete virtual machines from a friendly interface. Your end goal is this:

![booting-xp.png](/assets/blog/booting-xp.png)

And you can manage the machine from this slick interface:

![xp-performance.png](/assets/blog/xp-performance.png)

 Ready? Let's get started!

First we want to make sure your host processor can support virtualization. Open a terminal and run this command:

    egrep -c '(vmx|svm)' /proc/cpuinfo

If it returns 1 or greater your hardware will work with KVM. Depending on your computer manufacturer you may need to enable virtualization in your BIOS.

Now let's install the three pieces of software. In your open terminal window run this command to download and install:

    sudo apt-get install kvm libvirt-bin virt-manager

 Now go to your menu and open the newly installed Virtual Machine Manager. If it doesn't add to the menu automatically then type `virt-manager` in your terminal and then pin the icon to the menu manually. When it opens it should say `localhost (QEMU) Connecting` and then successfully connect. If it says `Not Connected` or when you try to create a machine you get `Error: no active connection to install on.` then you have one more step left to do. In your terminal type this command:

    sudo gedit /etc/group

In that file (probably on the last line) you will see a line that looks like this:

    libvirtd:x:127:

You want to add your username to the end of that line. So mine would end up like this:

    libvirtd:x:127:jerel

Now reboot your host machine. libvirtd is the api that Virtual Machine Manager uses to talk to KVM. Without your user being added to its group it doesn't have permission to connect. With this done you should now be ready to open Virtual Machine Manager and create your first machine. When Virtual Machine Manager opens click the icon in the top left corner and a wizard will guide you through creating your first machine. You will need an installation cd or an ISO image of the operating system that you wish to use.

Happy virtualizing and let me know how it goes in the comments below.
