---
layout: post
published: true
title: Generating a project changelog using Git log
date: 2011-07-27 03:00:00
categories:
- web-development
tags:
- changelog
- command-line
- git
- open-source

---

This is a simple and quick way to generate a changelog for your project using your git commit messages.

---


By default git allows you to format its log messages while it outputs them. By using this feature we can wrap our commit message, create a link to the actual commit, and all sorts of other fun. Then by running it through grep we can filter it further. Here is an example:

    git log v2.2.0...v2.2.1 --pretty=format:'<li> <a href="http://github.com/jerel/project/commit/%H">view commit &bull;</a> %s</li> ' --reverse | grep -v Merge

Output (link is 404):

[view commit](http://github.com/jerel) • Fixed a bug in the sympathy sector of the artificial intelligence module.

#### Now for a quick explanation:

* `v2.2.0...v2.2.1` tells git log that we only want it to return the commits between those two tags in our project.
* In the `--pretty=format:' ' ` flag we pass the html that we want generated for each commit using the `%H` and `%s` placeholders to output the hash and the message, respectively.
* `--reverse` outputs the commits in the order they were made instead of the most recent first.
* Finally with `| grep -v Merge` we pipe the output through grep and specify that we don't want the changelog cluttered up with merge messages (any message with the string "Merge" in it is discarded).

You could even stick this in a post-receive hook to generate a dynamic changelog. Or use grep to only show messages that contain a keyword (like github does with their "Closes issue #501" feature).
