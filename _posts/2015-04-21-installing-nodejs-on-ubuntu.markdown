---
layout: post
title: Tips for working with Node.js on Ubuntu
permalink: installing-nodejs-on-ubuntu
date: '2015-04-21 04:57:04'
tags: [Development, Ubuntu, Node.js]
cover: http://assets.boringgeek.com/imacDeskCodejpg
---

####Installing Nodejs from the standard Ubuntu repos.####
While this won't get you bleeding edge 0.12.x, it will it will get you a relatively recent and stable version of 0.10.x.  This probably isn't a bad thing as 0.12.x is pretty new and not everything supports it - yet.  

```
$ sudo apt-get update && sudo apt-get upgrade
$ sudo apt-get install nodejs
```

Important to note is that Ubuntu isn't going to call the binary "node".  Instead, its going to call it "nodejs".  Regardless of the logic behind this decision, its an easy fix by creating a simple symlink:

```
$ sudo ln -s "$(which nodejs)" /usr/bin/node
```

From there, most people are going to want to work with Node's package manager, NPM, so install this as well:

```
$ sudo apt-get install npm
```

At this point, you should be ready to rock with Node and NPM.  To validate your setup, feel free to check your versions.

```
$ node -v
v0.10.25
$ npm -v
1.4.21
```

In the event that you want to try a newer version of Node, I would recommend using the stellar PPA's provided by [Nodesource](http://nodesource.com).  Full instructions for 0.12 and even Io.js can be found here: [Node.js v0.12, io.js, and the NodeSource Linux Repositories](https://nodesource.com/blog/nodejs-v012-iojs-and-the-nodesource-linux-repositories)

If you're just getting started with Node, then I would recommend taking a look at the following resources to get you off to a good start:

* [Nodeschool.io](http://nodeschool.io/) - might take a few minutes to figure out the Workshops, but not a bad training method.  Gets you right into the code.
* [CodeSchool](https://www.codeschool.com/courses/real-time-web-with-node-js) - I'm a huge Pluralsight fan, so [CodeSchool](http://codeschool.com) always gets a bump from me.
* [Human Javascript](http://read.humanjavascript.com/) - While not a tutorial specifically on Node, it is a fantastic tutorial on building real apps with node and [Ampersand.js](http://ampersandjs.com/) and worth your time. The author ([@HenrikJoreteg](http://twitter.com/henrikjoreteg)) is an old acquaintance of mine and kind of a big deal in the JavaScript world. His [video tutorials](http://learn.humanjavascript.com) are awesome as well!

Good luck and happy coding!
