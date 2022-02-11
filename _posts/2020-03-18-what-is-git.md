---
layout: post
read_time: true
show_date: true
title:  What is git? and how to use it. Not really a complete guide.
date:   2020-03-18 15:14:20 -0700
description: "What is git?"
img: posts/gitbanner.jpg
tags: [git, microsoft, powershell]
author: Henry Dennis
toc: yes # leave empty or erase for no TOC
---

## What is Git?
Back around 2009 when i was graduated from high school and i have nothing to do, I was tinkering with linux and some other stuff thats called open source. And that time I was discovered git and github.

So, what it is? [What is git?](https://git-scm.com/), Taken from the website git is a free and open source distributed version control system designed to handle everything from small to very large projects with speed and efficiency. It outclasses every other SCM tools. 

Wait, what is SCM? SCM stand for Source Code Management. Well for some of you who are developer of some things either just do it for hobby, a school or uni project you really need management for the source code. To keep track for what you do before because no one get it rights for the first time. It is tool that keeps track of all changes, pushes and pulls code and a distributed repository which is used to store the code.

With SCM you can have control, fast delivery, and availability to manage your own code you develop.

The main difference is that CVS is (old) centralized version control system, while Git is distributed. Some of the features of git:

<ul>
<li><strong>System Compatibility –</strong> Git can run on any type of operating system. Linux, Windows, Mac, even on Bsd. As git has no problem pulling and pushing code to and from distributed repositories other than GitHub.</li>
<li><strong>Collaboration –</strong> It's allows for heavy collaboration. You can work together with your friends in project and you  can easily maintain a hierarchy and work on the same main code but on different snippets of features simultaneously and then merge all your work to form the software that is to be deployed. This allows for easy tracking of who is working on what part of the project and what changes they have made to that part.</li>
<li><strong>Speed –</strong> Git is very efficient at handling code and allows the user for easy forking and cloning of major repositories onto local systems.</li>
<li><strong>Security –</strong> It keeps a track of all the changes and commits that were made as logs.</li></ul>


## How Git works

You wrote your code on your local system and that code will then be pushed to a remote distributed repository on the internet. While Git, breakdown it in these code step.

## Git step.
### Step 1 - working directory

This the location where all of the working code will be stored, and this is where the user will be working from.


### Step 2 - intializing

This is when you wants to initialize your working directory. Initializing the directory tells git that this directory as a repository which can be later pushed onto the distributed cloud when needed. This is when all git starts tracking all the files in the directory. by using command prompt or terminal of your choice, you can established the command using;


```
$ git init
```

### Step 3 - staging

Staging code basically tells git where all the changes to the code are made and what kind of changes these are. i.e. additions & deletions are made. Then the code is **staged**. It can be done after you add your work into the folder by using git command;


```
$ git add *yourfilename
```


### Step 4 - commiting

All the changes made to the files to a local repository, saved onto the local system. For easy reference, each commit has a unique ID.


```
$ git commit *yourfilename -m "first commit"
```


### Step 5 - Pushing

When the code is pushed from the local storage to the cloud/distributed repository. With git it’s GitHub where the code is stored. Then the code can then be pulled later to make new changes and then pushed again repeating the life-cycle. Which can be done by using;

```
$ git push 
```


## Setting up git on windows 10 via terminal

<a name='Step1'></a>
### Step 1 - Download

Download latest version of git from the [website](https://git-scm.com/) install it as usual you do the installation. For the path choices choose **Use Git from the Windows Command Prompt.** 

<a name='Step2'></a>
### Step 2 - Check the version of git

Open up your terminal or powershell or cmd whatever then type

```
$ git --version 
```
It will show the current version you used on your windows machine.

<a name='Step3'></a>
### Step 3 - Configuration

if you already have your account on gihub then you need to add your account to the configuration by using these commands

```
$ git config --global user.name <yourusername>
$ git config --global user.email <youremail>
```

then check the configuration by using

```
$ git config --list
```

Congrats, you finally set up your git on your windows machine, for more git command and how to use it you can always google it. There's plenty of source on how to maximize you using git and github. As for this writing for my personal notes only. 

Cya!