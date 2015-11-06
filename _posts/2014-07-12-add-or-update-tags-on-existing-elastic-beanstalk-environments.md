---
layout: post
title: Add or Update Tags on Existing Elastic Beanstalk Environments
permalink: add-or-update-tags-on-existing-elastic-beanstalk-environments
date: '2014-07-12 21:33:00'
tags: [AWS, Tagging, Elastic Beanstalk]
cover: http://assets.boringgeek.com/imacSceneryReduced.jpg
comments: true
---

Back in May, Amazon introduced the ability to tag your Beanstalk environments. This is great for anyone that properly uses tags to monitor, track and account for the resources they are using.  One challenge that I've run into recently, though, is that this is only during the creation of an environment; AWS currently doesnt offer the ability to add, remove or update tags to existing environments.

For those of you that want to make that happen, here are some simple, albeit long, instructions on how to rotate environments out with new, properly tagged ones.

1. From the AWS Console, select Elastic Beanstalk from the Services Menu.
2. Click the name of the application you wish to tag. This will take you to the “Environments” view for this particular app.
3. From the “Actions” menu at the top right of the console, choose “Launch New Environment”.
4. Follow the wizard to spin up a new “B” environment for the specific environment you wish to update.
	- For example, if this were for a "NODE-APP-PRD" environment, create an environment titled “NODE-APP-PRD-B”.
	- Ensure the configurations matche that of the desired environment. (One thing you could do is save a configuration of the base environment.)
	- Ensure that the correct version of the code is deployed to this environment.
	- For this environment, do not worry about putting any tags in; this is simply a temporary space and you will terminate it once you're done.
5. Have the development team for the App test the environment and confirm functionality.
6. If everything is working as expected, click on the title of the new “B” environment to view that specific environment.
7. From the “Actions” menu on the top right, select “Save Configuration” to save a copy of the configuration. Name the configuration after the environment it is for.
8. From the “Environments” view for the application, click the “Swap URLs” button from the top right.
9. Select the correct two environments that you would like to swap and click the “Swap” button.
10. Once the URL swap is complete, have the development team test the app again and confirm functionality.
11. From the “Environments” view for the application, click the title of the original environment.
12. From the “Actions” menu on the top right and select “Terminate the original environment.
	- This will warn that the URL “NODE-APP-PRD-B.elasticbeanstalk.com” will be deleted.  This is ok; it's a temp URL anyway.  The real URL is currently assigned to the “B” environment.
13. Once the environment has been terminated, return to the “Environments” view for the application and click “Launch New environment” from the actions menu.
14. Follow the wizard to recreate the original environment.
	* Be sure to select the configuration you just saved.
	* Enter in the correct tags following the format that works for your organization.
15. Launch the new environment.
16. Once the environment is launched, confirm the configuration settings are correct and that the tags are showing up properly.
17. Have the development team confirm that the new environment is functioning properly.
18. From the “Environments” view for this application, select “Swap URLs” from the top right and swap the A and B URLS. This will point the original URL to the new, properly tagged environment.
19. Have the development team confirm that the environment is functioning properly
20. Terminate the “B” environment.

Thats pretty much it.  While there are a lot of steps, its pretty straight forward for an interim option while we wait for updated AWS functionality.  Another benefit of following this process is that it allows you to make the swap without any downtime for your end users.

If you have any suggestions or thougths on this, let me know in the comments!
