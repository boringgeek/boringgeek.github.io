---
layout: post
title: 'Workaround: MCrypt bug in Ubuntu 13.10'
permalink: workaround-mcrypt-bug-in-ubuntu-13-10
date: '2013-11-25 20:00:00'
tags: [Development, Bugs, Laravel, Ubuntu]
cover: http://assets.boringgeek.com/imacSceneryReduced.jpg
---

Having problems installing PHP5-MCrypt on Ubuntu 13.10?  You're not alone.  Apparently this is a bug with the latest version of Ubuntu, where they changed the default location for php modules, but mcrypt was apparently left behind. For full details and a workaround, feel free to visit this [Ubuntu Stack Exchange thread](http://askubuntu.com/questions/360646/cant-use-php-extension-mcrypt-in-ubuntu-13-10-nginx-php-fpm).
