---
layout: post
published: true
title: Korean QNIX IPS monitor and Apple displays working on Ubuntu Linux
date: 2013-10-28 04:00:00
categories:
tags:
- apple-display
- ips
- korean-monitor
- ubuntu

---

I recently bought a QNIX 2560x1440 27&quot; Korean IPS monitor to add screen real estate to my XPS 13&quot; ultrabook. Like most peripherals in Linux I expected it to just work when I plugged it in. Wrong. It did nothing at all.

---


When I tried to configure it via Ubuntu's Displays manager it just errored out. 

    The selected configuration for displays could not be applied

    could not assign CRTCs to outputs:
    Trying modes for CRTC 63
    CRTC 63: trying mode 1920x1080@60Hz with output at 1920x1080@60Hz (pass 0)
        none of the selected modes were compatible with the possible modes:
    Trying modes for CRTC 63
    Trying modes for CRTC 64
    Trying modes for CRTC 65

    CRTC 63: trying mode 1920x1080@60Hz with output at 1920x1080@60Hz (pass 0)
        none of the selected modes were compatible with the possible modes:
    Trying modes for CRTC 63
    Trying modes for CRTC 64
    Trying modes for CRTC 65

    CRTC 63: trying mode 1920x1080@40Hz with output at 1920x1080@60Hz (pass 0)
    CRTC 63: trying mode 1680x1050@60Hz with output at 1920x1080@60Hz (pass 0)
    CRTC 63: trying mode 1680x1050@60Hz with output at 1920x1080@60Hz (pass 0)
    CRTC 63: trying mode 1600x1024@60Hz with output at 1920x1080@60Hz (pass 0)
    CRTC 63: trying mode 1400x1050@60Hz with output at 1920x1080@60Hz (pass 0)
    ... and so on

So I started studying. I found [a post with a modified EDID file](/assetstely for me so I kept searching. Eventually I came across [a forum post about making Apple Cinema displays work on Ubuntu](http://ubuntuforums.org/showthread.php?t=1808585&page=2). Since these Korean IPS displays are actually Apple panels inside that got me to thinking that they probably are subject to the same trickery that Apple used in the Cinema panels and that this would just be a cleaner way to accomplish the same thing that the custom EDID did.

I used `cvt` to give me the correct modeline (my monitor is 2560x1440 and based on that forum post I used a 45 hz refresh rate).

    jerel@laptop:~$ cvt 2560 1440 45
    # 2560x1440 44.94 Hz (CVT) hsync: 66.52 kHz; pclk: 227.75 MHz
    Modeline "2560x1440_45.00"  227.75  2560 2720 2992 3424  1440 1443 1448 1480 -hsync +vsync

then plugged the monitor in to my DisplayPort and applied that generated modeline with xrandr. (don't copy my xrandr lines below as the modeline probably won't be right for your monitor. Use the value from your cvt command.)

    jerel@laptop:~$ xrandr --newmode "2560x1440_45.00"  227.75  2560 2720 2992 3424  1440 1443 1448 1480 -hsync +vsync
    jerel@laptop:~$ xrandr --addmode DP1 "2560x1440_45.00"

And it came to life!

![desk.jpg](/assets/blog/desk.jpg)

However this doesn't last through a reboot so I created an `xorg.conf` file so X can handle the display itself. Ubuntu 13.04 no longer needs an `/etc/X11/xorg.conf` as it auto detects everything so you'll most likely need to create the file.

Below is my minimal `xorg.conf`. I have the new Korean IPS plugged into a mini DisplayPort to active DVI adapter. If you want to see what your displays are named run `xrandr -q` Note: Do not use my modeline values, they will most likely not work. Generate your own with cvt and your display's resolution values as I did above

    Section "Monitor"
        Identifier "DP1"
            Modeline "2560x1440_45.00"  227.75  2560 2720 2992 3424  1440 1443 1448 1480 -hsync +vsync
            Option "PreferredMode" "2560x1440_45.00"
    EndSection

Reboot and the display should now be able to be hotplugged and can have its options (like turning off sticky edges and setting position) managed via the Displays window in Ubuntu.

I was concerned that I would come up with a solution that would apply the IPS settings anytime something was plugged into the DisplayPort and break projectors or other displays. However I've checked and this method allows my other displays to be detected as usual if I use one of them instead of the new IPS.
