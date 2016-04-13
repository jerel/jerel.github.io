---
layout: post
published: true
title: A simple solution to CodeIgniter CSRF protection and Ajax
date: 2012-03-16 17:00:00
categories:
- web-development
tags:
- codeigniter
- csrf
- pyrocms

---

We all know that we should enable CSRF protection if we want to make our apps resistent to cross site attacks. But what if we already have an Ajax heavy application with tons of POST requests? Do we want to go through the app and add code to each Ajax request? I don&#39;t. Here&#39;s a simple solution if you are using jQuery.

---


#### First the basics

You can read more about this with a quick Google search but here's a crash course: CodeIgniter sets a cookie with a hash as a value. Then when the page is loaded the `form_open()` helper adds a hidden input to your forms that contains the same value. When the form is submitted by the visitor that hidden input is compared with the cookie value. If they do not match the request is rejected before it ever reaches your controller. This keeps malicious users from submitting a request to your controller from outside your site, possibly by redirecting your own logged in browser.

Traditionally the solution is to read the cookie value before doing an Ajax request and then send it along. This works fine unless you have numerous places to add the code or if you are building an extendable system and other developers may not know how to solve this problem in their own code.

#### A solution

jQuery has the perfect tool for this job: `$.ajaxSetup`. You can read all about it here: jQuery documentation. `$.ajaxSetup` allows us to pass data along with every request so it is the perfect fit for sending our CSRF token. All post data from Ajax functions throughout your application will be merged with the data set by `$.ajaxSetup`. As far as they are concerned the CSRF token doesn't exist. The only thing needed for this to work is jQuery and the Cookie plugin. You can download the cookie plugin from here.

#### The code

    $(function($) {
        // this bit needs to be loaded on every page where an ajax POST may happen
        $.ajaxSetup({
            data: {
                csrf_test_name: $.cookie('csrf_cookie_name')
            }
        });

        // now you can use plain old POST requests like always
        $.post('site.com/controller/method', { name : 'Jerel' });
    });

Now if you did `var_dump($_POST)` in your controller at `site.com/controller/method` you would see "Jerel" and the csrf token (something like `3a92ba230fd952a2bcd6faa311b07015`).

#### Any catches?

Only one that I know of. It appears that the BlueImp uploader will not inherit the data from `$.ajaxSetup` so you will have to pass the cookie value manually in BlueImp as part of its data. I'm not entirely sure why this is as their documentation states that it uses jQuery's `$.ajax`. If you have an explanation let me know.
