---
layout: post
title: The Departure from Ghost to Jekyll
permalink: the-departure-from-ghost-to-jekyll
date: '2015-11-12 21:20:00'
tags: [Tips, Blogging, Jekyll]
cover: http://assets.boringgeek.com/closedLaptopWithGlassesOnDesk.jpg
description: "I've decided to get back into the swing of blogging, but have decided to depart from Ghost and focus on Jekyll."
comments: true
---

On a recent vacation to Yellowstone, I realized I would probably be without internet for a couple weeks... and like any tech addict, I scrambled to figure out how I was going to survive. I had already planned on fishing, reading and generally enjoying nature, but I needed something to do after everyone else was long passed out and snoring. It seemed like a tossup between sleeping or writing and since I'm terrible at sleeping, I went for writing. To be more specific, I decided to spend the time working on blogging again.

I've been meaning to play around with [Jekyll](http://jekyllrb.com/) for some time, but until now, I never really had a good enough reason.  It seemed fun and capable, but so was my existing blog powered by [Ghost](http:www.ghost.org).  So, what changed?  It's quite simple - lack of internet.  Like other blogging platforms, Ghost lacks off-line support. So, if I was going to make use of the time and actually write, I wanted something that would be there for me, when the internet was not.  Jekyll seemed to fit this bill perfectly.  

While at the airport, I quickly installed a copy on my laptop and scraped a copy of the help docs.  It took about twenty to thirty minutes to wrap my head around the process, but within the hour I was already a post deep and starting to tweak the theme a bit.  I remember looking over at my wife and mouthing 'this is great', to which she gave me her typical "yeah thats nice, you're such a nerd smile".  It truly was great.

Pretty much every night after everyone else had gone to bed, I had a good hour or two to myself to just write about anything I wanted.  It was quite freeing and Jekyll seemed to work like a charm.  I really enjoy markdown and the simplicity it offers for writing well structured content.  I also love the fact that my posts sit in a directory of tangibility (as tangible as digital good can get).  I can peruse them in [Atom](http://atom.io), I can pull them up on my local machine and preview them in the browser.  I have complete control over everything and tweaks are simple and don't require NPM and a litany of dependencies. To be honest, it feels more like making a static website, than working with a publishing platform.  And I think thats why I like it so much.

It follows a paradigm that is very comfortable to me and almost feels like second nature.  Everything down to the editor I'm using to write the posts follows the same workflow as any of the web applications I work on every day: write code, save, add, commit and push. In fact, its even the same mix of Atom, terminal, git and Github.  It just feels so natural and fluid.

This familiarity also carries over to hosting, which is another area where the processes starts to differ greatly from hosting a Ghost blog.  Instead of needing a Node.js environment, regular backups, version updates and security patches, Jekyll simply needs static hosting... in the truest form.  I'm talking no frills, super simple hosting.  The kind you find using an [S3 bucket](http://docs.aws.amazon.com/AmazonS3/latest/dev/WebsiteHosting.html) or [Surge.sh](http://surge.sh). You can even host it on Github.com with its [GitHub Pages](https://help.github.com/articles/using-jekyll-with-pages/) functionality (which also happens to support Jekyll).

The real wow factor is the shear simplicity of it.  I don't have to worry about managing an application environment.  It can't get hacked and I can host it completely free and place it behind the free tier of [CloudFlare](https://www.cloudflare.com/), caching the heck out of it.  For me, this is filled with win.

That doesn't mean it comes without its own trade-offs, though. This transition means that I have to manage a few more moving parts - that other bloggers don't typically have to focus on - such Git repos, command line tools and some basic ruby gems and node packages.  Additionally, I have to setup **_everything_** and am limited to what functionality can be achieved Jekyll plugins and the capabilities of the Liquid templating language. Thats not to say this is by any stretch difficult work, or even a ton of work, but its certainly _different_ work than many bloggers are probably used to and/or familiar with. So it's definitely something that needs to be considered when deciding to make this move.  Certainly know what you're getting yourself into and what you are giving up along the way.  

For me, the extra effort to get everything setup is pretty straight forward and analogous to the amount of effort needed to setup any blog initially. Additionally, once you've got it setup the way you want, you don't really need to touch those components and can focus on writing posts. So, this effort was perfectly acceptable to me and, so far, I'm thrilled with the results.

In a follow up post, I provide some step-by-step instructions on how to make the transition yourself and include the considerations for hosting, caching and even automating deployment on commit. Until then, hit me up in the comments if you have any questions.  
