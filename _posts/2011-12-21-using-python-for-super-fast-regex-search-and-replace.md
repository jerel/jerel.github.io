---
layout: post
published: true
title: Using Python for super fast regex search and replace
date: 2011-12-21 00:50:00
categories:
- web-development
tags:
- command-line
- python
- regex

---

I recently needed to do a regex search and replace on a large MySQL file. I often use my code editor for search &amp; replace but I tried Komodo Edit, Sublime Text 2, and Gedit and they struggled greatly to open the file and none of them could search it. I know there&#39;s sed, grep + awk, etc. but I decided to give Python a try since I&#39;ve been working at learning it.

---


I was amazed at how quickly Python searched the file. It hardly made a blip on my CPU usage. Here's what I used:

    [jerel@laptop ~] $ python

    # import the modules that we need. (re is for regex)
    import os, re

    # set the working directory for a shortcut
    os.chdir('/home/jerel/Desktop')

    # open the source file and read it
    fh = file('test.sql', 'r')
    subject = fh.read()
    fh.close()

    # create the pattern object. Note the "r". In case you're unfamiliar with Python
    # this is to set the string as raw so we don't have to escape our escape characters
    pattern = re.compile(r'\(([0-9])*,')
    # do the replace
    result = pattern.sub("('',", subject)

    # write the file
    f_out = file('test.sql', 'w')
    f_out.write(result)
    f_out.close()

This works fine for fairly large files altho I'm expecting that it wouldn't work for files in excess of 4GB on a 32 bit system as the entire file is read into memory. If you are working with massive files then it'd probably be wise to iterate over the file object or use the fileinput module. I'm not the guy to ask quite yet :)
