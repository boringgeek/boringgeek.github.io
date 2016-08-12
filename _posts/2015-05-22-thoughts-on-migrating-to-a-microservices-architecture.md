---
layout: post
title: Thoughts on Migrating to a Microservices Architecture
permalink: thoughts-on-migrating-to-a-microservices-architecture
date: '2015-05-22 16:25:32'
tags: [Development, Microservices, Architecture, Design Patterns, Operations]
cover: http://assets.boringgeek.com/closedLaptopWithGlassesOnDesk.jpg
description: 'Microservices are all the rage, but come with some serious considerations when moving from a monolithic architecture. Here are some things to keep in mind.'
comments: true
---

I was recently asked when it made sense to choose to architect an application based on micro-services vs the more common approach of using a more centralized core framework or codebase.  Answering this over Twitter is simply impossible as the answer isn't simple. In fact, there's a lot that needs to be considered as the decision extends well beyond just the code.  

>*Disclaimer: The following is by no way meant to be definitive. It's just meant to highlight some things you should consider*

##### So What are Microservices?
The idea is pretty simple: instead of building your application as one massive monolithic code-base, you break its core functions up into separate, smaller applications that operate as independent services. These services tend to perform a single specific function or purpose.  From there your app is a service itself that consumes the others.  There are a lot of good reasons for doing this, as I'm sure you can imagine.

Some highlights include:

* It allows for clear ownership of each service. This can help reduce issues when working with multiple development teams, as well as when the teams or ownership of a service changes. There is also less to ramp up on than a monolithic app.
* Speeds up development and release. As the services are independent, they can be developed in a loosely coupled manner and built, tested and deployed on their own schedules.
* Improves the ability scale efficiently; if one or two services are the ones getting heavy load, you can scale them independently instead of having to scale the entirety of a monolithic app.
* Allows you to use the right tool-set for the task. As they are independent, they can be built using a language, framework or set of utilities that best fulfills the required need.

With such fantastic benefits, it honestly sounds like the ideal way to build an application. Why would we every choose to build a monolithic app over this?

##### Considerations
As with any project we tackle in Applications Architecture - and even Solutions Architecture - there are a lot of things that need to be considered beyond just the code.  You have to consider development, infrastructure, operations, release management, security and networking among others. You have to look at it from a sunrise to sunset perspective. A microservices architecture is far more complex than a monolithic app and can be impacted by or have an impact on these teams and their abilities to support and manage the overall components.

The reasons for this are that when moving from one monolithic app to multiple smaller apps your're increasing the overall complexity of your environment. Think of it this way, while you may be separating concerns from a code and functionality perspective, you are creating a potentially large web of dependencies that now need to be accounted for at all layers of the "application".

To help frame this better, lets break it up into some logical groupings...

###### Infrastructure and Operations
At the foundation layer of the microservices stack is your I/O team. Honestly, this is the team that is probably hit the hardest by moving to a microservices architecture. The degree of complexity requires them to rely heavily on automation tools and the shift in architecture means they will need to manage the environments in ways they may not be used to. It may be a loaded term, but this is where the "DevOps" ideal starts to come in. In order to stand a chance of staying ahead of all this, your opts team will need to be tightly coupled with the dev teams building the applications. If anything, just to stand as a voice of sanity and reason.

**Load Balancing and Scaling.**  Depending on your Ops staff, this is a HUGE shift in mentalities.  Yes, you can strap a load balancer(s) in front of these services and scale horizontally (x-scale), but following a true microservices model may require your Ops team to explore both y and z-scaling.  The change is scaling and load balancing will most likely require using service discovery and load balancing tools like Zookeeper, Curator and Eureka.  Chances are, this is a whole new pattern for your Ops team (and Dev team). More on this later...

**Monitoring.**  Like the scaling challenge, configuring and maintaining monitoring for x<sup>n</sup> services adds to operational burden and additional load to your monitoring solution. This will also require a degree of scripting and automation to ensure that as services are scaled up and down, your monitoring solutions are updated to reflect the change.

**Operational Support.** As you can already tell... this is WAY more complex than a monolithic app to support, build, deploy, troubleshoot and maintain. Now... factor in that there is a chance that these services may be built using different languages, frameworks and services and you really need to take time to consider whether Ops staff can manage it all. Another reason why its good to join your Ops and Dev teams at the hip. That way, when one of the developers decides they want to built the next service in Go lang, they have to make eye contact with the Ops guy who is contemplating where they can hide their body.

###### Networking and Security
The next groups in the stack is your network and security teams.  While they don't have it as bad as I/O, they certainly do have their work cut out for them.

**Bandwidth.** Networking requirements also need to be considered as what was previous 1 request within a monolithic app now can spider into x<sup>n</sup> requests between the various services. Ensuring this happens with as little latency as possible is important, but we'll get into that a bit more later...

**Firewall.** As you're going from one application to many services, you have to consider which services get exposed externally and which are kept internal. These decisions require the firewall rules and security groups to become more complex than with a single application, which segues nicely into...

**Application Security.** As you now have multiple services with varying degrees of external exposure, you now have a much larger target for potential attackers.  The fact that these services could potentially span multiple languages, frameworks and OSes increases the complexity for your security team to successfully perform security audits and penetration tests against the services.

**Threat Management**  As there is now a lot more communication happening between all of these potential services, there is a lot more for your security team to need to monitor and be aware of.  This makes discerning between expected traffic and rouge requests more complex and where dependency mapping tools like Neebula (Service Watch) can really help.

###### Development
At the top of the stack you have the development teams. While they certainly get some wins when moving to this architecture, there are still a number of things they now need to consider that may not have been an issue with a monolithic architecture.

**Caching.** As there is now more chatter between services developers have to find ways to limit the number of requests that they need to may.  One obvious way to account for this is with caching of requests. The ideal would be to cache wherever possible and try and refresh data across as many services as possible in one request. While this is something any developer worth his salt should be doing regardless of app architecture, caching requests across multiple services that you may or may not manage can become complex and require coordination across services and the teams building them...

**Use of request headers.**  In addition to caching, developers have to address how they handle sending data (such as authentication details) between services that need it to reduce the number of requests. One way of doing this is through the use of request headers.  Imagine needing to call 5 services that all require authentication. Each of those services will need to call the authentication service to validate you are authenticated. Or, a better way would be to include your auth details in the header of each request so that the services can check for it and only call the authentication service when its not there. Again, like caching, doing this across multiple services that you may or may not manage may require coordination with other teams.

**Latency.** With increase in the number of requests, developers need to account for the time it takes for these requests to be processed; serialization and deserialization of requests to x<sup>n</sup> services can add up. We'll discuss why this can be bad next...

**Resilience and Fault Tolerance** As this architecture is one based on a web of interdependence, developers must go above and beyond to build the services to be fault tolerant and resilient to failures, timeouts and etc. This is an order of magnitude more challenging than with a monolithic app. If requests to one service start to back up, this could easily cause others to back up which could lead to a domino effect of failures cascading across your services or even triggering of unnecessary scaling of the services.

##### That's great. Answer the question already!
So when should you choose to build a solution based on microservices instead of a monolithic app?? The answer is simple - *it depends!*

Yeah, you should have seen that coming a mile away.  The answer, as should be clear from all this lead up, is that it depends on a number of things:

* The purpose of the application and its intended use and audience
* The makeup of the team that is going to be developing it
* The capabilities of the infrastructure, operations, networking and security teams
 that will need to scale, support, monitor, maintain and secure it

The reality, if its not painfully obvious, is that building an app using a microservices architecture can be a lot of work and requires doing things in different ways. I'm not saying "Don't do it! It's Hard!", I'm saying ensure that the benefits outweigh the costs and that you're doing it for the right reasons; because the application needs it, it will help you ship faster, ensure stability and/or create a stable platform for your customers to build off of.

###### Additional Resources
Thinking about taking the plunge? Like DevOps, Microservices are all the rage right now and there are TONS of resources to help you along the way.  Good luck.

* [Microservices at Netflix - Lessons for Team and Process Design](http://nginx.com/blog/adopting-microservices-at-netflix-lessons-for-team-and-process-design/)
* [Microservices at Netflix - Architectural Best Practices](http://nginx.com/blog/microservices-at-netflix-architectural-best-practices/)
* [Microservices at Netflix - Best Practices and Tools of the Trade (Video)](https://www.youtube.com/watch?v=LEcdWVfbHvc)
* [Microservice Patterns](http://microservices.io/patterns/index.html)
* [Using Containers to Build a Microservices Architecture](https://medium.com/aws-activate-startup-blog/using-containers-to-build-a-microservices-architecture-6e1b8bacb7d1)
* [Microservices - Not a Free Lunch](http://highscalability.com/blog/2014/4/8/microservices-not-a-free-lunch.html)
* [Microservices - The Good, The Bad and What You Could Be Doing Better](http://nordicapis.com/microservices-architecture-the-good-the-bad-and-what-you-could-be-doing-better/)
* [Building Microservices at Karma](https://blog.yourkarma.com/building-microservices-at-karma)
