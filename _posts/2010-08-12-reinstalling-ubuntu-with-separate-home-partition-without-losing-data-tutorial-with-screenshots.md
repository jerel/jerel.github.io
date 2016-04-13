---
layout: post
published: true
title: Reinstalling Ubuntu with separate home partition without losing data. Tutorial with screenshots.
date: 2010-08-12 19:10:00
categories:
tags:
- linux
- tutorial
- ubuntu

---

I recently updated my aquarium computer from Ubuntu 9.04 to 10.04. I have a separate home partition for my data and wanted to make sure it survived untouched. I didn&#39;t see any step by step instructions so I decided to write a tutorial in the hopes that it will help someone else.

---


Get a live cd or a live usb stick to install from. Just a note: I needed a usb drive to install from and found that usb-creator is an excellent tool from creating a bootable install usb drive.

1. Create the bootable usb drive to install from: `sudo apt-get install usb-creator`
2. Run it from the terminal: `usb-creator-gtk`
3. Select your downloaded ISO or your live cd
4. Select the destination zip drive that you want to put Ubuntu on.
5. You should now have a shiny new live usb drive!
 
#### Install Ubuntu without disturbing your old /home partition.

Insert your usb drive or the live cd that you want to install from. You may need to hit F12 to when your computer boots and select the media that you wish to boot from.

Boot up all the way and then select Install from the desktop. I got a "irrecoverable error" when I did this so you may have to go to System->Administrator->Install. That worked for me. When you get to the partition manager part of the install select Specify partitions manually.

![specify_partitions_manually.jpg](/assets/blog/specify_partitions_manually.jpg)

Then choose your root partition. My root partition was 15GB as you can see. Click change.

![change_partitions.jpg](/assets/blog/change_partitions.jpg)

For the root partition select format and set the mount point as /.

![edit_root.jpg](/assets/blog/edit_root.jpg)

Now select the home partition and click change. Here is the critical part: Do not select format. Select the mount point as /home.

![edit_home.jpg](/assets/blog/edit_home.jpg)

 I formatted my swap partition but I've gathered that it's not necessary. Just select the partition as swap and it should be good.

Here is the final screen before the install starts. Check everything carefully! The home partition should *not* be in the list of partitions that will be formatted!

![install.jpg](/assets/blog/install.jpg)

 There you go! You should have a nice new install of Ubuntu and still have all of your old data. (of course if you don't have don't blame me)

Feel free to let me know how it went in the comments.
