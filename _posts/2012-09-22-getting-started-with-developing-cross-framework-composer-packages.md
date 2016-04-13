---
layout: post
published: true
title: Getting started with developing cross-framework Composer packages
date: 2012-09-22 22:10:00
categories:
- web-development
tags:
- composer
- open-source
- packagist
- psr-0
- tutorial

---

There&#39;s a bit of a renaissance going on in the PHP world right now, and it&#39;s a very good thing. Many of us developers are used to identifying ourselves as being a &quot;CodeIgniter developer&quot; or a &quot;CakePHP developer&quot;, or maybe even a &quot;Zend developer&quot;. The new PHP-Fig standards and a beautiful package management system named Composer may change all that.

---


#### How and why?

If you are a CodeIgniter developer this will look very familiar to you:

    $this->load->library('upload');
    $data = $this->upload->do_upload();

Now let's say you use the CodeIgniter upload library and you like the features it has. But you don't like anything else about CodeIgniter. Now what? You could convert the library to use camelCase instead of snake_case and figure out how to load it in your framework and you would end up with a port that would work (once you replaced all the little framework dependent bits inside the class).

But now Joe that uses the Acme framework sees it and he likes the upload library but nothing else about your framework. So he ports it to the Acme framework... Ultimately many hours of labor have been wasted with no real gain. From now on you each maintain your separate copies and add features that you need with little profit from each other's labors.

#### The Solution

Enter PSR-0, the interoperability standard. The PSR-0 convention guarantees that classes written for one project can be dropped into another project and their namespaces and classes will coexist happily with all your other PSR-0 code. Since the directory structure must match the namespacing it makes autoloading a breeze.

#### The Tools

Now it's time to talk about [GetComposer.org](http://getcomposer.org) and [Packagist.org](http://packagist.org). Composer is a wonderful tool for installing and updating all of your PSR-0 packages independently. Packagist is Composer's directory that tracks those packages so that developers (and Composer) have a single place to look for compatible code.

Now when you decide to write the world's best upload package you write it to match the PSR-0 style, push it to Github, and tell Packagist.org about it. Joe from the Acme framework wants to use it so he installs it with Composer and everyone is happy. You keep improving the package but Joe doesn't need to worry, he specified in his composer.json config file that his project needs Uploader v1.0.x so when he runs `composer update` he will get bug fixes but no breaking changes.

#### Testing

There is another very real benefit to using Composer packages. Every package that you use should have its own test coverage. Now instead of installing some random class that you found on the internet and wearing out the Ctrl + R on your keyboard working out the bugs you can install a package knowing that it is stable. I use phpUnit along with Guard to run my tests as I work.

#### I'm sold! How do I start?

It's actually quite simple to develop packages for use with Composer. The first thing you will need is to read through the PSR-0 guide so that you understand the namespace style and the autoloading.

Now install Composer. You can find instructions at getcomposer.org. On my Linux machine I just run:

    curl -s https://getcomposer.org/installer | php

Now create a folder to hold your Composer packages and inside that create a composer.json file. This file is used to specify which packages the project will need. We want to install our new package inside this project but since our new package won't be submitted to Packagist until it is finished we need to specify a Github repository for Composer to fetch it from.

    {
        "repositories": [
            {
                "type": "vcs",
                "url": "http://github.com/jerel/upload"
            }
        ],
        "require": {
            "jerel/upload": "master"
        }
    }

That's it for the project file itself. Now we are ready to make the first files of the actual package that we're creating. To do this first init a Git repository (it can be anywhere, it doesn't need to be in the project folder yet) and create a composer.json file in its root. Then open the file and make it look something like this:

    {
        "name": "jerel/upload",
        "type": "library",
        "description": "This is the world's best upload package!",
        "keywords": ["upload", "more upload"],
        "homepage": "http://github.com/jerel/upload",
        "license": "MIT",
        "require": {
            "predis/predis": "v0.7.3"
        },
        "authors": [
            {
                "name": "Jerel Unruh",
                "email": "some-email@jerel.co"
            }
        ],
        "require": {
            "php": ">=5.3.0"
        },
        "autoload": {
            "psr-0": {"Jerel": "core/"}
        }
    }

You will notice that this composer.json requires the predis package. We might want to use Redis for a resizing queue so we just tell Composer that anytime it installs our upload package it also needs to install the predis package. Isn't that cool? You don't have to tell other developers to install a bunch of different packages, if they install yours Composer will automatically install everything that it needs.

Now commit this file and push it to the repository that you specified in the project's composer.json file (the first one you created). Now that it is pushed to the master branch of that repo you will be able to install it via Composer. `cd` to the project directory and run `composer install`

This will create a directory structure like this: `project/vendor/jerel/upload/` Now you can `cd` to `project/vendor/jerel/upload` and start writing your code. You will notice that in the package's composer.json there is this line `"psr-0": {"Jerel": "core/"}`

This tells Composer that when the `Jerel` namespace is used it should use the `project/vendor/jerel/upload/core` folder as the root to load classes from. So `project/vendor/jerel/upload/core/Jerel/Upload.php` will be autoloaded when you use `$uploader = new Jerel\Upload;`

That's about it.

Create a `tests` folder for your unit tests in `project/vendor/jerel/upload` and go to town with your development. When you have published a package to Packagist.org you can remove the `"repositories": [ ]` info from your project's composer.json as Composer will search for the package name on Packagist.org by default. So any later projects you do can use your awesome uploader library by simply placing this in a composer.json file and running `composer install`:

    {
        "require": {
            "jerel/upload": "1.0.*"
        }
    }
