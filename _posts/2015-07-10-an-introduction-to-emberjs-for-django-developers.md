---
layout: post
published: true
title: An introduction to EmberJS for Django developers
date: 2015-07-10 21:00:00
categories:
- web-development
tags:
- django
- ember
- tutorial

---

Back when I was in the automotive industry I noticed a stark difference between the engineering of domestic and import (especially German) vehicles. While they look much the same on the outside, under the hood they use a very different approach. On German cars hoses are connected with quick-connect latches and wire connectors have twist locks. American cars use worm or squeeze clamps on hoses and pinch locks on wires. The contrast between single page applications and server applications are much the same: to the consumer they are similar but working on one requires us to use the opposite side of our brains. As is the case with cars once you learn to think in the same manner as the original engineers everything just makes sense. This post is an attempt to explain some of the things I&#39;ve learned the hard way over the last several years.

---


![ember.png](/assets/blog/ember.png)
![django.png](/assets/blog/django.png)

#### We're not building "pages"
The first concept to grasp is that we don't serve a fully formed HTML page at every URL (except in the case of Universal JavaScript apps, but we'll leave that topic alone for now). Instead we serve the same basic HTML page that includes our framework assets and our app's JavaScript code. This page is often just a static file that is served by Nginx or Apache for every URL. The app then "boots" taking over the screen and rendering the HTML from minified templates and JSON data. It uses the URL segments to determine what to display.

#### The client app doesn't care about your server framework
Don't think of client side apps as an extension of your server side. Usually they are separate (I store mine in separate git repositories) and only communicate via JSON over an API. You can even serve your client app from a CDN. If your server side can parse and render JSON it will work with Ember, React, native applications, etc. If you do embed your client side app inside a server rendered page then you can share its session authentication but I have had good luck with authentication via tokens just as you would authenticate a mobile app.

#### Client apps are long lived
When you're using jQuery plugins to decorate a server rendered page you can count on a page refresh once in a while to clear memory and reset state. I have one Ember app that runs on a wall mounted monitor in a dispatch office and is only refreshed for upgrades: memory leaks will not go unnoticed here! Use long-lived-state to your advantage, if data is fetched when the app first loads it will stay in memory until released so there's no need to make an ajax request each time it is needed. This can also present challenges when API updates are made as a client may keep using the old API for days.

#### Beware of hype
Don't write a website as an isomorphic JavaScript application that talks to a Go API over websockets just because it's the next big thing. I wrote this blog on Ember a couple years ago because I wanted to experiment with it. Would I write everything as a client side app now? No. But if it's an application that a customer uses all day every day or you need to do something very complex then client side applications are amazing.

#### The client side is complex
When you have a long lived client talking to a stateless API (and maybe a websocket) you are working with one of the harder problems in computer science: distributed computing. Give yourself time and treat it like any other development field. You won't learn iOS development in a week and you won't master Ember in a week either.

#### Terminology
Remember, we need to think with the other side of our brain here. Some of the same words are borrowed from server frameworks but they mean different things when in the context of a client application.

* `router.js` - this file maps URLs to routes. It is the equivalent of the urls.py in a Django application
* `route` - somewhat equivalent to Django views or Django REST Framework resources. They have a `model` function that is called when the route is entered and that function needs to return data (usually fetched from the API via ajax). Routes handle navigating from one state to another (and keep you from breaking the Back button).
* `model` - the client side representation of your data (I use Ember Data, an excellent project). Models in my apps often somewhat match Django models but they aren't 1 to 1. Model instances have a `save()` method on them and generally work a little like you'd expect a Django model to. Relationships can be fetched via `instance.get('relation')`.
* `component` - components have two pieces `acme.js` and `acme.hbs` and are most similar to Django template tags. The hbs file is in handlebars format and contains HTML that is compiled and shipped in the `client.js` file. The component's js file handles the interactivity aspect, data manipulation related to display, and DOM events. The hbs file is optional as sometimes you may want a component to handle a single DOM element. Rather than setting up jQuery to listen to DOM events you will use `{{action 'example'}}` in the hbs file to tell Ember to call the `example` action function in your component when the element is clicked, touched, etc.
* `adapter` - Ember Data uses an adapter to specify how to talk to an API. If you follow conventions by using a package like [Rest Framework Ember](https://github.com/django-json-api/rest_framework_ember) you will rarely write an adapter.
* `serializer` - Ember Data also uses serializers to munge data from one format (a non-standard API) to the format expected by Ember Data. Once again if following conventions you will rarely write a serializer.
* `controller` - you may see controllers referenced in old documentation. Ember is moving away from them as they were poorly named. Controllers were simply long lived objects and can be replaced with components (if the use case is related to the DOM) or services (for the shared long lived object aspect).
* `view` - views are also a relic of the past. Views used to play the role that components now do except they were more "course" as they were typically paired 1:1 with routes.

#### Development and building
The Ember community is standardized around the Ember CLI for development, package management, and asset building. This makes it very very simple to start new projects and introduce additional developers to a project. File structure, build pipeline, asset management, it's all provided out of the box. Install it with `npm install -g ember-cli` and you're ready to create an app with `ember new app-name`. You can even proxy all calls from the development domain to your Django install to avoid CORS errors by running `ember serve --proxy=http://127.0.0.1:8000`. Ember CLI provides live reload, file watching, and building. After running `ember build` you will find everything that you need to deploy in the `dist` folder.

#### Shared effort
Much like pypi Ember developers can combine efforts by sharing Ember Add-ons. Browse [Ember Observer.com](http://emberobserver.com) to see the many add-ons that you can install. Calendar widgets, loading indicators, animation, deployment, there's a lot there.

This is a short blog post for such a huge topic but hopefully it will help you in the right direction. Let me know if there's specific aspects you'd like me to write about in depth and I'll attempt to cover them in future posts.
