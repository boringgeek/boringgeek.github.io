---
layout: post
title: 'How to Setup Ghost on Elastic Beanstalk'
permalink: how-to-setup-ghost-on-elastic-beanstalk
date: '2014-07-22 07:00:00'
tags: [AWS, Elastic Beanstalk, Ghost, RDS, EC2, SES, EBS, Operations]
cover: http://assets.boringgeek.com/imacSceneryReduced.jpg
comments: true
---

Over the past six months, the [Ghost blogging platform](http://ghost.org) has made some huge waves in the blogging world. It has a responsive, streamlined interface that gets out of the way and enables authors to blog quickly and efficiently. At a time when [WordPress](http://wordpress.org) is growing more and more complex, this is a breath of fresh air. And, even in its pre-1.0 release status, the platform is undoubtedly stable and usable.

For those of you who want to run it in a production level environment, this post will walk you through the process of getting it up and running on Amazon Web Services; providing speed, stability, monitoring and security.

To get started, you will need access and familiarity with the following AWS tools and services:

1. AWS Console
2. Elastic Beanstalk - for hosting and monitoring the blog
3. RDS - for the MySQL database
4. SES - for sending email (optional if you have your own SMTP service)
5. EBS - for ephemeral storage
6. IAM - to allow access to mount EBS volumes

###Setup Simple Email Service
The first step in this process is to get yourself setup with SES. This will be your blogs email mechanism and will allow you to track things like bounces, complaints and successes all from with the AWS console. Now, to be honest, you could just use your GMail account or any other SMTP server you access to.  However, if you're planning on making your blog a success - getting the stats on bounces and complaints is worth setting up SES.

####Setup
1. Created a Verified Email
 1. Log into the AWS console and select the SES service from the menu
 2. Under the "Verified Senders" heading on the left navigation, click "Email Addresses"
 3. If you don't have a verified email address listed, click the "Verify a New Email Address" button and enter your email address.
 4. Click the "Verify This Email Address" button and Amazon will send an email to the address you entered.
 5. Open your email an click the verification link.
2. Create your SMTP Credentials
 1. From the SES Console in AWS, click the "SMTP Setting" link in the left nav.
 2. Click the "Create My SMTP Credentials" button. This will take you over to the IAM Console and show a wizard for creating a user for SMTP
 3. Give the IAM User a name. I put "SES-SMTP-USER" and deleted the date stamp.
 4. Click the "Create" button.
 5. This will create the user and ask you to download their credentials. **It is important that you put this somewhere safe**; you will only get to do this once. If you lose these credentials, you'll need to delete the user and create a new one.
3. Request Production Access.
 1. If you don't already have production access to send email via SES, you'll need to request it.  From the SES Dashboard, click the "Request Production Access" button at the top of the screen.
 2. This will take you to a support screen to request a "Service Limit Increase".  Fill out the form with accurate information and submit it.  They will review your request and either approve it or contact you with any questions.

###Setup Persistent Storage
By default, Ghost stores images and other data within the `content` directory.  However, since we're using Elastic Beanstalk, if your instance becomes unhealthy, AWS will kill it - and take that `content` directory with it.  To get around this, we're going to use a combination of EBS and ebextension configs to mount persistent storage and symlink those directories over to it. To get started we'll need to create an EBS volume.

####Create an EBS Volume
1. From the EC2 console, click the `volumes` link on the left navigation.
2. Click the `Create Volume` button.
3. Follow the wizard to create a volume
	- **Take note of the availability zone you select**, you will need this later when setting up your elastic beanstalk environments
    - Size the volume using your best judgment, for my purposes 5GB was adequate. Changing this later is possible, but its not as simple as increasing the size.
4. When the volume has been created, **take note of the `Volume ID` as you will need this later on in the process**.

Now you have a volume, however, this is basically the equivalent of adding a drive to your own computer; without formatting that drive, nothing can use it.  In order to format the drive, you will need to [attach it to an EC2 Instance running Linux](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-attaching-volume.html), [SSH in to that instance](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AccessingInstancesLinux.html) and [format it](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-using-volumes.html) by doing the following:

1. From the command prompt, use `lsblk` to list the drives attached to the instance
2. Find the device name for the drive you need to format
3. Run `sudo mkfs -t ext4 device_name` swapping out `device_name` for that of your particular device.
4. Logout of the shell and unmount the volume from that instance. If you created that instance for this step, now is a good time to terminate the instance as well.

####Create an IAM User
In order to allow our scripts to mount the EBS volume, we will need to create an IAM user and grant it the specific permissions needed to accomplish this.

1. From the IAM Console, click the `Users` link on the left navigation
2. Click the `Create New Users` button
3. Provide a username for the user. I used `ghost-ebs-user`
4. If its not checked, check the "Generate an access key for each User" checkbox.  We will use these access keys in our scripts to mount the drive.
5. Click the `create` button.
6. Click the `Download Credentials` button to download a CSV of the users credentials.  This will be the only time you can do this; forgetting them, will require you to generate new ones. **Keep these handy as you will need these for a later step**
7. Click `Close Window`.
8. Select the checkbox next to the newly created user
9. In the user properties window on the bottom half of the screen, click the `Properties` tab.
10. Click the `Attach User Policy` button
11. Select the `Policy Generator` option and click the `Select` button
12. Under the `AWS Service` dropdown select `Amazon EC2`
13. Under `Actions` select the following options

    	AttachVolume
        DescribeVolumeAttribute
        DescribeVolumeStatus
        DescribeVolumes
        DetachVolume
14. For the `Amazon Resource Name` enter `*`
15. Click the `Add Statement`


###Prepare Ghost for Installation
Based on our configuration within AWS, we need to make some minor tweaks to our Ghost codebase before we can begin the installation.

####Download Ghost
1. Down load a copy of Ghost from the [Ghost download page](https://ghost.org/download/)
2. Unzip it somewhere convenient for you to access it

####Modify the Default Node.js HTTP Port
In order to get node working in Beanstalk, you will need to ensure it using the correct port: `8081`. This is the port used by Elastic Beanstalks HTTP nGinx web server.

1. From within the Ghost directory, create a copy of `config.example.js` and name it `config.js`.
2. Edit the `server` section of the file and change the port reference from `2368` to `8081` in both the "Development" and "Production" sections of the file.

#####Modify the Default URL
In order to actually use this as a production instance, you will need to update the `config.js` file to reflect your desired url for your blog.

1. Edit `config.js` and update the `url` value in both the `development` and `production` sections of the file to reflect the correct url
2. Save the file.

####Ensure the Proper Node.js Version
Ghost is built to run on Node.js version `0.10.*`, but Beanstalk will default to `0.8.*`.  To ensure that your Beanstalk Environment provisions with the proper version, we will need to instruct it to via EB's "[ebextensions](http://docs.aws.amazon.com/elasticbeanstalk/latest/dg/customize-containers.html)" functionality.

1. In the root of your ghost directory (the one with "content" and "core"), create a new folder called `.ebextensions`.
2. In the `.ebextensions` direction create a config file called `00_ghost.config` and put the following in it:

        option_settings:
  		- namespace: aws:elasticbeanstalk:container:nodejs
    		option_name: NodeVersion
    		value: 0.10.21

This config will get executed during the provisioning of your environment and ensure that your Node.js server starts up with the correct version that Ghost needs.

####Configure the EBS Mount
As we have the EBS volume and user permissions created, we need to setup another extensions config script that will attach the volume at boot. To do this, we will use an undocumented post-deploy hook that will run the script after the application has been deployed.  This is essential as this depends on symbolic links that point `/var/app/current/content/data` and `/var/app/current/content/images` to `/var/app/data/` where we will mount our ebs volume.

1. Create another file in your `.ebextensions` folder called `01_mount_volumes.config` and put the following into it:

		files:
  		/opt/elasticbeanstalk/hooks/appdeploy/post/99_ephemeral_storage.sh:
    		mode: "000755"
    		owner: root
    		group: root
    		content: |
      		#!/usr/bin/env bash
      		export JAVA_HOME=/usr/lib/jvm/jre && \
      		export EC2_HOME=/opt/aws/apitools/ec2 && \
      		export DATA_DIR=/var/app/data && \
      		export IN_USE=$(/opt/aws/bin/ec2-describe-volumes --region us-west-2 --hide-tags \
      		--aws-access-key [[YOUR-AWS-ACCESS-KEY]] \
      		--aws-secret-key [[YOUR-AWS-SECRET-KEY]] \
      		[[YOUR-EBS-VOLUME-ID]] | grep "in-use") && \
      		if [ -z "${IN_USE}" ]; then
      		/opt/aws/bin/ec2-attach-volume --region us-west-2 [[YOUR-EBS-VOLUME-ID]] \
        	-i $(/opt/aws/bin/ec2-metadata --instance-id | cut -c14-) \
        	-d /dev/sdh \
        	--aws-access-key [[YOUR-AWS-ACCESS-KEY]] \
        	--aws-secret-key [[YOUR-AWS-SECRET-KEY]] &&
        	sleep 30 && \
        	if [ ! -d "${DATA_DIR}" ]; then
                mkdir -p ${DATA_DIR}
        	fi
        	mount /dev/xvdh ${DATA_DIR} && \
        	sleep 30 && \
        	if [ ! -d "${DATA_DIR}/content" ]; then
                mkdir -p ${DATA_DIR}/content
        	fi
        	echo "Creating persistent Ghost data directory" && \
        	if [ ! -d "${DATA_DIR}/content/data" ]; then
                mv /var/app/current/content/data ${DATA_DIR}/content/.
        	else
                rm -R /var/app/current/content/data
        	fi
        	echo "Creating persistent Ghost images directory"
        	if [ ! -d "${DATA_DIR}/content/images" ]; then
                mv /var/app/current/content/images ${DATA_DIR}/content/.
        	else
                rm -R /var/app/current/content/images
        	fi
        	chown -R nodejs.nodejs ${DATA_DIR} && \
        	echo "Symlinking data directory" && \
        	ln -sf ${DATA_DIR}/content/data /var/app/current/content/. && \
        	echo "Symlinking images directory" && \
        	ln -sf ${DATA_DIR}/content/images /var/app/current/content/.
      	fi

Be sure to update it with the following information:
1. Replace `[[YOUR-EBS-VOLUME-ID]]` with the volume ID of the EBS instance you are using.
2. Replace `[[YOUR-AWS-ACCESS-KEY]]` and `[[YOUR-AWS-SECRET-KEY]]` with the ones from the `ghost-ebs-user` you created earlier.

Also, if you copy and pasted the script from above, it may be a good idea to paste it into a [YAML validator](http://yamllint.com/) to ensure the format is good; things like an extra `tab` will cause all kinds of headaches.

####MySQL RDS Configuration
1. Edit the `config.js` file
1. Under both the `development` and `production` sections of the config:
 1. Delete the "sqlite3" connection string:

  			database: {
            	client: 'sqlite3',
            	connection: {
                	filename: path.join(__dirname, '/content/data/ghost-dev.db')
            	},
            	debug: false
              },

   2. Replace it with the RDS connection strings:

   			database: {
		    		client: 'mysql',
	    			connection: {
	        			host: process.env.RDS_HOSTNAME,
	        			user: process.env.RDS_USERNAME,
	        			password: process.env.RDS_PASSWORD,
	        			database: 'ebdb',
	        			charset: 'utf8'
	    			},
	     			debug: true
 			 },

   3. Save the File
3. Edit `package.json` to remove the Sqlite requirements.
 1. Under the `dependencies` section delete the line for Sqlite.
 2. Save and close the file.


####Configure the SMTP Settings for SES
1. Edit `config.js`
2. Delete the commented mail section under Development and Production.  Replace it with the following:

		mail: {
    		transport: 'SMTP',
    		host: 'YOUR-SES-SERVER-NAME',
        	options: {
            	port: 465,
            	service: 'SES',
            	auth: {
                	user: 'YOUR-SES-ACCESS-KEY-ID',
                	pass: 'YOUR-SES-SECRET-ACCESS-KEY'
            	}
        	}
        }
3. Be sure to replace the following sections with your SES information
	1. Host - replace this with the "Server Name" listed on the SES Consoles "SMTP Settings" page.  Mine was `email-smtp.us-west-2.amazonaws.com`, but yours will vary based on the region you configured SES in.
    2. user and pass - these will be found in the `credentials.csv` that you downloaded during the SES setup above.
4. Save the file and close it.

####Package it all up
Now that the necessary config has been done, we need to zip up the directory and get it;ready for uploading to Elastic Beanstalk.

To do this:

1. Create a zip of your ghost installation. You will want the configuration files and the ghost `core` and `content` folders to be at the root level of the zip.

###Setup and Deploy to Elastic Beanstalk
Now that you have all the preliminary work done, we can go ahead and start setting up our hosting.  

1. From the Elastic Beanstalk Console, click the `Create a New Application` button
2. Name the application accordingly. I used `Ghost Blog`.
3. Click `next`
4. From the `Environment tier` dropdown, select `Web Server`
5. From the `Predefined configuration` dropdown, select `Node.js`
6. From `Environment type` choose `Load Balancing, autoscaling`
	- This is important.  While we will only have a single instance, the load balancer and autoscaling group will help ensure that if anything happens to the health of that instance, it will be terminated and replaced.
7. Select `Upload your own` and choose your previously created zip file of the Ghost install
8. Click `Next`
9. Name your environment as you choose and click `Next`
10. Select the option to `Create an RDS DB Instance`
11. Select the option to `Create this environment inside a VPC`
12. Click `Next`
13. Select your instance size. You can change this later. I started with a `t1.micro`
14. Select your EC2 key pair.
	- if you don't have one created, open a new tab and create one from the `Key Pairs` link on the left navigation of the EC2 Console. Come back to this tab and then click the refresh link and it should show up.
15. Fill out the remaining fields as desired and click `Next`
16. Tag your environment as you see fit and click `Next`
17. Select the instance class for your RDS instance.  For my example I chose `db.t1.micro`.
18. Allocate at least 5GB of storage
19. Enter a username
20. Enter a password
21. As the goal is to make this redundant
	- Choose `Create Snapshot` from the `Retention setting` dropdown
    - Choose `Multiple Availability Zones` from the `Availability` dropdown
22. Click `Next`
23. Choose your VPC. I left mine as default
24. For your ELB, EC2 and RDS, you need to select the same availability zone as the one you used for your EBS Volume.  For your RDS, you will also need to select an additional AZ as it requires two.
25. Click `Next`
26. Review your settings and click `Launch`.

Now sit back and wait. It will take about 20 minutes for your RDS instance to come up.  When it does, you should see a green health status for your Beanstalk environment and clicking its URL should show you the default Ghost blog homepage.

You will note that the URL does not match the one you put in your `config.js` though. Instead, the url will resemble something along the lines of `my-app-env.elasticbeanstalk.com`. This is expected. To use your own domain name, you will need to create a DNS CNAME the points your domain to the Elastic Beanstalk URL. Instructions on this will vary based how you manage your DNS Service. I recommend using CloudFlare, detailed below.

###Next Steps
So now that you have your blog up and running, you could just stop there.  However, if you really plan on **blogging**, you should consider doing some additional steps to protect your blog from hackers, make it go faster and let it scale - for when it become really popluar.

####CloudFlare
For those of you who aren't familiar with [Cloudflare](http://www.cloudflare.com), its a great - *free* -  service to add on top of any site that helps speed it up, keep it safe and reduce load.

	Note: CloudFlare replaces your current DNS service, so you'll need access to your domain registrar to update your name servers.

Here is a high-level overview of some of its benefits:

1. **Speed**.  It acts as a content delivery network for your site and keeps cached copies of it on its servers around the globe.  Your visitors will get the copy that closest to them ensuring the fastest pageloads possible.
2. **Overhead**.  If the bulk of your content is static, then the majority of your traffic will be served by the caches. This means your server will only see cache refreshes or dynamic content requests and you can size your instances smaller than if they had to server it all. And we all know smaller instances cost less!
2. **Security**. As all traffic routes through them, they scan it for known threats and block them before they reach your servers.  This is a huge deal as protecting your site from being hacked will help ensure a happy blogging experience for both you and your readers.

####Centralized Storage
One of the downsides to Elastic Beanstalk is that the storage is not persistent and its not shared.  We solved this for a single instance environment using our second EBS volume for our content directory. However, if you scale your site up beyond a single instance, this won't work. As this affects one of our goals of scaling with demand, this is definitely no bueno. But have no fear, using [S3FS](https://code.google.com/p/s3fs/wiki/FuseOverAmazon), you can mount an S3 bucket as your content directory.  This works *pretty well* - so long as you don't use AWS's US-EAST-1 region - and will allow your site to scale to multiple instances without issue. If the load of your site demands, I would highly reccomend doing this.

####Monitoring and Logging
As with any production-level environment, its always a good practice to setup proper monitoring, alerts and logging.  Elastic Beanstalk make this quite straight-forward as you get basic infrastructure monitoring out of the box.  I would recommend taking some time to setup the basic thresholds and alerts to notify you of any issues that may arise.  You can also enable CloudWatch Logging through an ebextensions config that will send all your system logs to CloudWatch where you can create custom alerts based on events within your logs. [Here is a good walkthrough of the process](http://aws.amazon.com/blogs/aws/cloudwatch-log-service/).

Couple these with Google Analytics within your theme or through CloudFlare and you'll have everything you need to monitor traffic and performance for your blog.

###Conclusion
If everything went correctly, you should now have a stable, durable blogging environment that you can grow with your demand. I hope you found this walkthrough useful.  If anything, its a great way to exercise your knowledge of the AWS stack and its many useful services.

If you have any questions or suggestions, feel free to use the comments below or hit me up via [Twitter](http://twitter.com/boringgeek).

####Sources:
- [Six Steps to Deploy Ghost to AWS Elastic Beanstalk](http://blogs.aws.amazon.com/application-management/post/Tx37ALIK2KLNIVC/Six-Steps-to-Deploy-Ghost-to-AWS-Elastic-Beanstalk)
- [Installing Ghost & Getting Started](http://docs.ghost.org/installation/deploy/)
- [Using Amazon RDS with Node.js](http://docs.aws.amazon.com/elasticbeanstalk/latest/dg/create_deploy_nodejs.rds.html)
- [Mount EBS Volume to Amazon Elastic Beanstalk Environment](http://www.jcabi.com/jcabi-beanstalk-maven-plugin/example-ebs-volume.html)
- [OpenShift Ghost Quickstart](https://github.com/openshift-quickstart/openshift-ghost-quickstart/blob/master/.openshift/action_hooks/deploy)
- [Undocumented post-deploy hooks](https://forums.aws.amazon.com/thread.jspa?messageID=493887)
