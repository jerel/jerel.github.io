---
layout: post
published: true
title: Connect to a remote Elixir node deployed with Distillery
date: 2016-12-09 02:27:00
categories:
- web-development
tags:
- elixir
- phoenix
---

When building Elixir apps with Distillery I don't install Elixir on the server as Distillery bundles everything it needs.

---


I found that I always had to google and fiddle around a bit to connect Observer to a running app so decided to document it clearly once and for all.

#### The Code

Most existing tutorials or docs that I found assumes that epmd is accessible on the remote. This method needs nothing but standard Linux tools.

``` bash
me@local:~$ ssh user@example.com
user@app1:~$ netstat -ntlap | grep LISTEN
tcp        0      0 0.0.0.0:4369            0.0.0.0:*               LISTEN      3443/epmd
tcp        0      0 0.0.0.0:39566           0.0.0.0:*               LISTEN      3439/beam.smp
tcp        0      0 0.0.0.0:5432            0.0.0.0:*               LISTEN      3548/postgres
tcp        0      0 0.0.0.0:5000            0.0.0.0:*               LISTEN      3439/beam.smp

# on local machine use ports from top two rows above create two tunnels (3rd row is postgres and 4th is web app)
me@local:~$ ssh -L 4369:localhost:4369 -L 39566:localhost:39566 user@example.com
user@app1:~$

# on local machine in a different terminal
me@local:~$ iex --name debug@127.0.0.1 --cookie your-cookie # found as -setcookie in rel/<app>/var/vm.args
iex(debug@127.0.0.1)1> Node.connect(:"my_app@127.0.0.1") # found as -name in rel/<app>/var/vm.args
iex(debug@127.0.0.1)1> :observer.start

# in the Observer window that opens you can now select the remote node from the Nodes menu
```

#### Results

![elixir observer](/assets/blog/observer.png)

If you have a better / more concise way of connecting let me know.

