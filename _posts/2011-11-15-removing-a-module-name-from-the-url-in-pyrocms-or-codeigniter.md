---
layout: post
published: true
title: Removing a module name from the url in PyroCMS or CodeIgniter
date: 2011-11-15 02:00:00
categories:
- web-development
tags:
- codeigniter
- pyrocms

---

Let&#39;s say we are writing a module named &quot;vacations&quot; that will display information about tourist destinations. Sometimes it&#39;s desirable to display your urls like: site.com/bahamas instead of site.com/vacations/bahamas. So here&#39;s how you can do that in more than one module...

---


In the main application route file you will need to change one line. Open `system/cms/config/routes.php` and set `$route['404_override']` to 'vacations' or whatever your module name is. By default it is set to 'pages' in PyroCMS.

Now when a visitor tries to view the page called site.com/contact he will get that page just like usual if it exists. But when he visits site.com/bahamas CodeIgniter will first turn the request over to the default controller (pages) which attempts to find a page with that uri. When no page is found it continues on to the 404 handler which is now our 'vacations' module. Now we have an opportunity to retrieve information about 'bahamas' from the database and display it. Everything works as if "vacations" was set as the default module.

However since our module is set as the `404_override` it's our responsibility to show a 404 when no information is found to match the uri segment within our module. This can be done by using `show_404('404');`

I've found that this works quite well and the only "waste" is the one query that the Pages (or default) module makes to check for a page with that uri.
