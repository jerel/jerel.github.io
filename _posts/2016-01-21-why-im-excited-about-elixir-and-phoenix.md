---
layout: post
published: true
title: Why I'm excited about Elixir and Phoenix
date: 2016-01-21 02:10:00
categories:
tags:

---

For the last couple years I&#39;ve been writing Python for my server side applications. I really enjoy Django and Django REST Framework and how they enable me to build rapidly. However the last few months I&#39;ve been studying the Elixir language and its main framework, [Phoenix](http://www.phoenixframework.org/). This post explains why I&#39;m betting heavily on it for the future.

---


To start off with I'll explain how I need to use my frameworks and then I'll follow up with an explanation of how Phoenix approaches those problems better.

#### The Problem

I have a SaaS application that provides soft real time GPS tracking for public safety departments. The server receives GPS and other data streams from various hardware on ambulances or personnel and broadcasts them to the correct clients. The client applications stay open for weeks at a time and constantly receive data over websockets.

Now, using a traditional web framework such as Django presents a problem: how do you handle long lived, highly concurrent connections using a framework designed around the classic request/response? Initially developers added bidirectional communication to their applications by subscribing to a service such as Pusher. That's reasonable for some apps but not for something that you are building an entire business on. You can add websockets in front of your HTTP framework using something like [Hendrix](https://github.com/hendrix/hendrix) or Tornado but based on my experience getting good performance isn't easy and most libraries that you are used to reaching for are blocking. You could write everything in Node and either just use one of your server's CPUs or write userland code to leverage them all. Or you can split your codebase (microservices amiright?) letting your favorite framework handle API calls and Node handle your websockets.

In my architecture I went with the last option and it's good but not great.

#### Let me introduce Elixir and OTP

Elixir is a dynamic, functional language that compiles down to run on the Erlang VM. It was [created by José Valim](https://en.wikipedia.org/wiki/Elixir_(programming_language)) and shares some similarity to Ruby, syntactically.

> Any sufficiently complicated microservices deployment contains an ad hoc, informally-specified, bug-ridden implementation of half of [OTP] -- [@littleidea](https://twitter.com/littleidea/status/532927711472549888)

Let me follow that up with another quote:

> If you watch the software industry backwards, it starts with kids flailing; ends with old guys solving impossible problems by thinking hard. -- [@garybernhardt](https://twitter.com/garybernhardt/status/152455259543961600)

It turns out that lots of the problems that the web is running into today have already been solved. Erlang was created by Ericsson back in '86 to run telephone networks (when's the last time your phone was "down for routine maintenance"?) and was open sourced in '98. One of the greatest strengths of Erlang (and by extension Elixir) is concurrency. Back in the 80s they didn't have phenomenal CPUs like [the Xeon E7](http://accessories.us.dell.com/sna/productdetail.aspx?c=us&l=en&s=&cs=04&sku=319-2142&dgc=ST&cid=293344&lid=5616479&acd=12309152537461010&ven1=sE1inYhPj&ven2=,#Overview) but they did have lots of slower ones so Erlang and OTP (the set of libraries supporting deployment, distribution, etc) were designed to scale horizontally.

Consequently today it is trivial to spread the processing of an Elixir application across many CPUs and many machines. Anything that takes a significant amount of time (like a web request or a websocket) is done in its own super lightweight process (an Erlang VM process; not an operating system process). Then when that task is finished the process is garbage collected. If you need to do slow work like wait on an external API then just do it! It will only hold up your own process. A single server can run hundreds of thousands to millions of processes easily with the work being distributed across all available cores. If one client does something that creates an error only that process dies and restarts, not your entire app!

#### And now let's talk about Phoenix

[Phoenix](http://www.phoenixframework.org/) is the framework built to leverage and simplify all of this. One of the biggest features in my mind is Channels which is a thin abstraction on top of websockets (it provides a keep alive heartbeat, hooks like join, handle message, etc). Channels aren't tacked on as an after thought, they are core to the framework and can be reasoned about much like HTTP requests. Phoenix Channels ship with websocket and longpolling transports (with client libraries to match) but you can completely swap them for a custom solution of your own such as a UDP transport.

Phoenix also provides the things you would expect in a framework like code generators to help you get started quickly, migrations, security helpers like CSRF, form helpers, templating, etc.

A Phoenix application is just another OTP app. If you're working on a large project dealing with all sorts of different protocols and one of them just happens to be the web you're in luck. Or if you're building a simple blog and don't have a clue what OTP is you're still in luck.

Phoenix uses the concept of a "connection", usually referred to in code as `conn`. When an HTTP request comes into the app a `conn` data structure is created and is passed through the app being transformed by the framework functions first (request headers read, origin checked, request body parsed, etc) and then by your code (data inserted to the database, flash messages set, etc) until the response is created and the `conn` returned to the browser. _Channels are handled in much the same way_ only instead of a stateless request/response they are a stateful conversation between client and server. With channels the `conn` exists for the duration of the websocket connection so you can store data on it such as a user ID or permissions.

Phoenix is functional. Every backend framework I'd used before was object oriented and I assumed functional programming, the GNU project, and Gentoo had a lot in common. It's turning out to be quite user friendly and really nice... as mentioned in the example above you have data (a connection) that you perform transformations on until it reaches the state you want. In Phoenix these steps of transformations are called Plugs which are Elixir modules with functions `init` (compile time) and `call` (run time) defined. Plugs are not entirely unlike middleware in other frameworks but in Phoenix almost _everything_ is a plug. CSRF protection in the framework? A plug. Body parsing in the framework? A plug. Authentication in your code? Write a plug. Permissions? Write a plug. Do you want different permissions in a couple controllers? Include a plug in those controllers. If you don't like something that the framework does then swap out that plug. You starting to get the picture? :) And if at any point you wonder what the state of your app is you can `IO.inspect(conn)` and everything is there, as data.

#### In closing

Today's internet is more than just a network of documents. It still does that too but it's now also expected to do telephony, control medical equipment, stream movies, and talk to your thermostat. As we web developers assume more of that responsibility lets remember to look away from the tools that we've used for years on CRUD apps and evaluate ways that may be better.
