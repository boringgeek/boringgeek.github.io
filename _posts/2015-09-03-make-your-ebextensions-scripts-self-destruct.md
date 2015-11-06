---
layout: post
title:  "Make Your .Ebextensions PowerShell scripts \"Self Destruct\""
date:   2015-09-15 11:48:00
tags: [Tips, Development, Elastic Beanstalk, PowerShell]
cover: http://assets.boringgeek.com/imacDeskCodejpg
---

If you're using Amazon's PaaS offering, [Elastic Beanstalk](https://aws.amazon.com/elasticbeanstalk/), you're more than likely using .EBExtensions to control the configuration or other aspects of your applications.  And I'm sure, As any responsible devloper/admin would, you're cleaning up after yourself as well... right?

In the event that you aren't, its not tough to start.  In fact, here is the PowerShell code you need to have your scripts remove themselves after they've completed their duties.

```Remove-Item -LiteralPath $MyInvocation.MyCommand.Path -Force```

Just throw that in as the last line of your PowerShell script and they'll run through their task and then - *\*poof\** - no more script!

This is particularly useful for things that are only needed on initial deployments, or only need to be run once.  I personally use it to setup scheduled tasks on the initial deployment.
