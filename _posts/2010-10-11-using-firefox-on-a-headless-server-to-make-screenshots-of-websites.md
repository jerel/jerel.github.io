---
layout: post
published: true
title: Using firefox on a headless server to make screenshots of websites
date: 2010-10-11 15:30:00
categories:
tags:
- command-line
- firefox
- linux
- tutorial

---

I needed a reliable way to generate screenshots of websites for a client&#39;s project I&#39;m working on. I wasn&#39;t too wild about using a third party like thumbalizr or webshots if I could do it myself. I decided to detail my ordeal here in the hopes it will help somebody else...

---


I'm using Xvfb + Firefox + Imagemagick on a Ubuntu server to accomplish the task. In case you're not familiar with them Xvfb allows Firefox to output just like it would on a monitor even though there is no screen. Imagemagick then grabs the screenshot from Firefox. And if you don't know what a server is then this probably isn't the solution for you :)

Install Xvfb, firefox, and imagemagick

    sudo apt-get install xvfb firefox imagemagick

Start Xvfb with the desired screen dimensions on virtual display 1 (1280x960 gives an image with an aspect ratio of 4:3)

    Xvfb :1 -screen 0 1280x960x24 &

Open desired url in Firefox on virtual display 1

    DISPLAY=:1 firefox http://www.google.com &

Grab a screenshot of the window using imagemagick's import feature, crop it, compress, and save thumbnail as screenshot.jpg in the screenshots folder of the server.

    DISPLAY=:1 import -window root -crop 1264x948+0+0 -resize 200x150 -quality 90 /var/www/screenshots/screenshot.jpg

 You should now have a screenshot of firefox + google.com
Now... modify firefox so the browser frame and navigation bar is hidden.

Create a new firefox profile on a local computer.  Install the autohide addon from http://www.krickelkrackel.de/autohide/ *Update: After working with this some more I decided to do it without any help from addons. After hiding all bars just adjust the imagemagick crop dimensions to remove the top of the browser window.* Tweak firefox until all toolbars, status bar, etc is hidden when you open the browser. Turn off addons updates etc. Basically turn everything off besides javascript. Now copy the firefox profile folder to your server.

Firefox also was not maximized by default. So I edited localstore.rdf around line 30 and changed the main-window dimensions to match that of Xvfb

To keep firefox from trying to restore the last session add `user_pref("browser.sessionstore.resume_from_crash", false);` to the prefs.js

Now for a summary. Here's how to run all of the commands at once:

    DISPLAY=:1 firefox http://www.cnn.com  & sleep 5 && DISPLAY=:1 import -window root -crop 1264x948+0+25 -resize 200x150 -quality 90 /var/www/screenshots/screenshot.jpg && pkill firefox

 The "pkill firefox" is to make sure firefox is closed after the screenshot is generated. Otherwise you will end up with many many tabs open.

And this is what we get for our efforts:

![screenshot generated with ubuntu server + firefox](/assets/blog/screenshot.jpg)

If you have any improvements or questions please leave them in the comments.
