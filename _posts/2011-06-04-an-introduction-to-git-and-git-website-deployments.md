---
layout: post
published: true
title: An introduction to Git and Git website deployments
date: 2011-06-04 17:00:00
categories:
- web-development
tags:
- command-line
- git
- tutorial

---

This post is an introduction for web developers who may have never used version control before. Learn how to control your websites&#39; source code and even deploy the site without using ftp.

---


*Note: All commands that I have posted are from the Linux command line. If you are on a different platform you'll need to make adjustments where necessary. Most of the workflow on your local machine can also be done with a gui interface so feel free to do so. I also assume that you have ssh access to your web server.*

#### Get Git

First you will need to install the git software on your local machine and on your server. You can download it from git's download page or by using your system's package manager:

    sudo apt-get install git-core

#### Creating your first project

Your project files can be anywhere on your computer, git doesn't care. To create a new project (called a repository) you need to cd to the root of your source code and init a git repository. Like this:

    cd /var/www/public_html/my_website
    git init

Alternatively you may want to clone an existing repository from somewhere. In that case do not run the git init command. Use git clone instead.

    cd /var/www/public_html/
    git clone git@github.com:organization/my_website.git

Now if you look for a hidden folder in the root of that directory there will be one named .git. That folder will contain all of the history, the source for the different branches, etc. If you want to move your project to a different directory or even rename it it's no problem. Just make sure the .git folder moves with it. As long as the .git folder stays safe you can even delete all the files for your project and they can still be restored using "git reset".
 
#### Adding the files

After initializing a new git repository you must tell git which files you want it to track (cloning has this step done already). You can do this via the command line using the "add" command.

    git add --all

This will make git start tracking all files in the project directory. If you don't want to add all of the files you can specify the file name instead of the --all flag. You can also create a .gitignore file in the root of your directory that specifies which files git should leave alone. You may want to add upload folders, cache folders, etc. to the .gitignore file.

#### The first commit

Git now knows that the website files exist but it is not controlling them. To do this you need to do a git commit:

    git commit -a -m 'This is the first commit.'

The -a flag means that you want to add all files that git knows about into the index. The -m flag is for the commit message.
 
#### Pushing to a server

The first way that we'll talk about is pushing to a user account named "git" on your remote server. The repositories you push there will be for collaboration and backup purposes. For example if Jim and Bob both work from home one can edit files, make commits, push to the server and the other can pull from the server, edit more files, make his own commits, and push back. And if both of their laptops were destroyed at the same time all they would have to do to get all of their work onto the new machines is do a git clone on the remote repositories.

To get this working you'll need to ssh into your server. Add a user account named "git" (or whatever you like) and set up ssh keys on that account for everybody that will have access to the repositories. Now create a folder, cd to it, and initiate a bare git repo. You must use the --bare flag when creating a repo that users will pull from.

    mkdir /home/git/my_website.git
    cd /home/git/my_website
    git init --bare

Now you are ready to add your server as a remote (a remote is shorthand so you don't have to type the whole path every time). And push your files. When you push you need to specify the branch name (usually master until you get more advanced and start using additional branches).

    git remote add origin ssh://git@example.com/home/git/my_website.git
    git push origin master

#### Pushing to deploy a website

The second way that we'll talk about is deploying the site using git. It is very similar except the remote repository must be configured a little differently. Ssh into your server and create the folder in the web directory. Then you need to init a non-bare repository just like you did on your desktop. That's important! A bare repository like you created in the first server example does not have a copy of the directory tree. The file information is all stored within git. That will not work for a live website.

    mkdir /home/client/my_website
    cd /home/client/my_website
    git init

We now need to do a couple tweaks to the git config on the remote server to allow us to "overwrite" the files when we do a git push. First open /home/client/my_website/.git/config in your favorite editor and add the following code to the bottom:

    [receive] denyCurrentBranch = false

Then create /home/client/my_website/.git/hooks/post-receive, paste this into it, and make sure the file is executable:

    #!/bin/sh
    # Update the working tree after changes have been pushed here
    cd ..
    env -i git reset --hard

Alright! Now we just need to set up the local workstation to push it live. What we're doing here is adding another remote. You'll still have the "origin" remote that you use while you're working but when you are ready to update the site you will push to live instead of origin.

    git remote add live ssh://git@example.com/home/client/my_website
    git push live master

 You can also repeat this process if you want a staging site. Then all you have to do is "git push staging master", let your quality assurance team or your client review it, and then "git push live master".

#### Working with an upstream repository

Now supposing you cloned a content management system and you want to keep your website up-to-date you can add a git remote called "upstream". This allows you to pull the latest release from their repository, merge it into your copy, and push it to live. I'll give an example using PyroCMS. (This is all done on your workstation)

    cd /var/www/public_html/my_website
    git remote add upstream git@github.com:pyrocms/pyrocms.git
    git pull upstream tag/v1.2.0
    //do some testing locally
    git push live master

#### Working with a GitHub fork

If you have forked a project on github then you can send code back to the main project via your fork. Just add the fork as a remote just like the "Pushing to a server" example.

    git remote add fork git@github.com:username/project.git
    git pull upstream master
    git push fork master
    //you now know that your fork is up-to-date. Time to make your edits.
    git commit -a -m 'I fixed a bug for you guys.'
    git push fork master

Now go to your GitHub control panel and open a pull request to let the team know that your fork has code that they want.

One important note: you will not always push your contributions to the master branch. For example on the PyroCMS project most bug fixes need to be on the develop branch as the master branch is reserved for releases. Check the documentation for the project you are contributing to. Second more important note: I used the `git commit -a` command in the example. Make sure that you do not commit sensitive data (like database passwords) and push it as it then becomes public.

#### Merges, conflicts, and how to handle them

If there is more than one person working on a project soon you will have a merge conflict. When you start your work you should always pull from the shared repository. This gets the latest edits from your team members and reduces the number of merges. Now suppose you pulled, edited the index.php file, committed it, and pushed, all would be fine unless someone else happened to do the same thing and pushed before you did. If they pushed first then you will get an error like this:

    To git@example.com/home/git/my_website.git
    ! [rejected] master -> master (non-fast forward)
    error: failed to push some refs to 'git@example.com/home/git/my_website'

If this happens then you need to pull again. Git will then attempt to merge the files together. If the same file has been edited by multiple people then it will place markers in the file to show which lines are older and which are newer and then warn you that the merge failed. You then open those marked files with your editor and decide which lines you want to keep. Commit the files now that the markers are removed and then push again.

#### Closing thoughts...

 I'm aware that there's many many ways to do things with git. Especially site deployments. However this method seems the simplest to me and doesn't require much setup. If you can ssh to the account you can use git to manage the files. I'd also like to give credit to Phil Sturgeon as he has a [blog post](http://philsturgeon.co.uk/blog/2010/02/Deploying-websites-with-Git) about deploying with this method. I know this isn't an exhaustive guide but hopefully it will get you started in the right direction.

If you need better control over who has access to the repositories on your own server check out gitosis. It allows you to manage your repositories via git itself. You can allow some users to read-only, some to read-write, etc. Also you may find it worthwhile to buy a GitHub subscription instead of hosting your own repositories.

When you get into branching I would recommend reading about [git-flow](http://nvie.com/posts/a-successful-git-branching-model/). It is a wonderful way to organize branches and it helps to make sure the code you release is as stable as possible. (We use it on the PyroCMS project. That's why code contributions are not welcome on the master branch)
