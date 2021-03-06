---
layout: post
title: Create an ".EBextensions" Folder on Windows
permalink: create-an-ebextensions-folder-on-windows
date: '2015-05-11 19:29:03'
tags: [Tips, Elastic Beanstalk, Development]
cover: http://assets.boringgeek.com/imacSceneryReduced.jpg
description: "Hidden folder that start with a period are common on Linux, but not on Windows.  Here&#x27;s how to create an .ebextensions folder for use with Elastic Beanstalk"
comments: true
---

I have a number of Linux-based applications that have been using the .ebextensions functionality of Elastic Beanstalk for quite some time. Its a great way of managing the configuration of your environments and running tasks both pre and post-deploy. However, one of my current projects is a C# .NET app and it took me a while to remember how to create a folder in Windows with a leading (.) period.  It seems so trivial, but, honestly, I can't recall the last time I've had to do it - chances are many of you can't either.

So here it is in all of its strange simplicity:

<code>.ebextensions.</code>

Yes, you're seeing that correctly - simply add a trailing (.) period and Windows will create the folder and remove the trailing period.

Enjoy!
