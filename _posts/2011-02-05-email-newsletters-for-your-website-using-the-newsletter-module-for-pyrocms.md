---
layout: post
published: true
title: Email newsletters for your website using the Newsletter module for PyroCMS
date: 2011-02-05 00:30:00
categories:
- web-development
tags:
- codeigniter
- email-marketing
- pyrocms

---

v1.0 of the Newsletter module was released on February 7, 2011 and adds an exciting new functionality to the PyroCMS platform.

---


As of v1.0 the Newsletter module has these features:

* Email templates (comes with 4 already installed)
* Track unique & total clicks on embedded links
* Track unique & total newsletter opens
* { pyro: Tags are available in email body
* Subscribers can unsubscribe with a single click
* Optionally unsubscribe an address from the admin panel
* Export subscriber list from admin panel
* Send newsletters from your browser or with a cron job
* Separate From and Reply addresses
* Limit the number of emails sent at once (for picky servers or ISPs)

All template and email editing is done with the familiar WYSIWYG editor. Images can also be uploaded and inserted into the email body using the editor. Requires minimal configuration to get started... if you can send emails with the Contact form your server can also send newsletters.

Screenshots:

![create.png](/assets/blog/create.png)
![send.png](/assets/blog/send.png)
![settings.png](/assets/blog/settings.png)
![templates.png](/assets/blog/templates.png)

You can easily automate the sending of emails by setting up a cron job. For example if you were to use curl your command might look like this:

    //send at 1am every night
    0 1 * * * curl --silent --compressed http://www.example.com/newsletters/cron/gy84kn

 If you had a large mailing list and couldn't send the whole list at once you could simply set a limit in Settings->Newsletters and set multiple cron jobs:

    //send at 1am
    0 1 * * * curl --silent --compressed http://example.com/newsletters/cron/gy84kn
    //send again at 1:10am
    10 1 * * * curl --silent --compressed http://example.com/newsletters/cron/gy84kn
    //and again at 1:20am
    20 1 * * * curl --silent --compressed http://example.com/newsletters/cron/gy84kn

[You can buy a copy of it here.](http://pyrocms.com/store/details/newsletters) If you have more questions or if you have feature requests for future versions let me know in the comments.
