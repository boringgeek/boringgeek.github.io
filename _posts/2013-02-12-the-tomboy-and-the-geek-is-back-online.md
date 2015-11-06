---
layout: post
title: The Tomboy and The Geek is Back Online!
permalink: the-tomboy-and-the-geek-is-back-online
date: '2013-02-12 20:00:00'
tags: [Goals, Development, WordPress]
cover: http://assets.boringgeek.com/imacSceneryReduced.jpg
comments: true
---

I have finally gotten [The Tomboy and the Geek](http://www.thetomboyandthegeek.com) back online! This is third site I needed to restore and is right in-line with my [Year of the Hustle post](/the-year-of-the-hustle/). This particular site is meant to help keep our friends and family in the loop with what we've been up to. The site previously housed years worth of pictures and content, so it will take a while to get the old content (as much as we still have) republished and available.

As for the nerdy details - the site is powered by [Wordpress](http://www.wordpress.org) (naturally), is hosted on [Pagoda Box](http://www.pagodabox.com) and behind a [Cloudflare CDN] (http://cloudflare.com). In addition to that, the site is running a Redis cache that is storing both the session states and the content. I'm making nightly backups of the site which are emailed offsite and then stored in secure cloud solution. The content is also being abstracted to other providers (Vimeo, YouTube, Picasa Web Albums, etc) and simply being referenced. In this regard, if there ever is another incident, recovering should be as simple as a Git push and a DB restore. Lets hope I never have to test that.
