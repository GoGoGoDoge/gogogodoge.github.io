---
layout: post
title: "Git-Entry-Level-Instruction"
date: 2017-08-19
---

<!-- ## {{page.title}} ## -->

This blog will include:
* git clone
* git pull
* git checkout (with -b or not)
* git add
* git commit -m
* git push


---
## git clone ##

This is the first step you can copy the repository(files and directories) from remote (github) to your local machine.

1. Go to github and click the clone or download and copy the link

    ![copy the path](/files/git-clone.png)

2. Go to your local folder location and and type `git clone (link copied)`. Then you will clone the repository from remote your local folder.

---
## git pull ##

This command is used to help your local repository be consistent with latest update in the remote repository. Use it before you want to make some changes to current branch.

The `git status` command can show you what branch you are in and any update you have not commit (which I will introduce later). As shown below, it is in the master branch and there are files I have not yet commited.

![git status](/files/git-status.png)

---
## git checkout ##

Before you shall do anything, it is highly recommended you switch to one of branch that you are responsible for.

Use following command to create a new branch, if use without -b, then you switch the created branch.
> git checkout -b <your new branch name>

For example:
> git checkout -b xmeans-distance

Now I create a new branch there and at the same time, you also have the full copy of original master branch's files and folders.

![git checkout](/files/git-checkout-b.png)

## git add ##

Now you can create and edit your local file!

First I create a README description file here. The suffix "md" means it is a markdown format file, where you can enjoy the elegant of markdown.

> touch README.md

We use `git add .` to add whaever in the folder, this is lazy but quick.

> git add .

## git commit -m ##

However, we also require to commit what we write (just like confess to your gf you never eat her snacks) and admit the change.

![git commit](/files/git-commit.png)

Actually, you may want to config your git locally and make it consistent with remote repository.

## git push ##

Now we can push, but you create a new branch locally and have not informed the remote repository. Git will complain and just do what it requires.

For example here:
> git push --set-upstream origin xmeans-distance

![git push](/files/git-push.png)

Otherwise you can just push:
> git push

---
Now everything settled down. Let us have fun with Professor Shin's project, my big leg.
