---
id: 126
title: Things You Should Know Before Choosing Bluemix
date: 2016-04-30T08:21:49+00:00
author: Ian
layout: post
guid: http://www.clianz.com/?p=126
permalink: /2016/04/30/things-you-should-know-before-choosing-bluemix-3-pros-and-cons/
mytory_md_path:
  - 
mytory_md_text:
  - 
mytory_md_mode:
  - url
categories:
  - Bluemix
  - Cloud
---
Bluemix is a cloud service provider that let will host your web app for you on the cloud. Similar to Amazon’s AWS, Microsoft’s Azure, Google’s Cloud Engine, Saleforce’s Heroku, Redhat’s Openshift, etc.
<!--more-->

  * **Based on Clound Foundry** – Bluemix can provide container hosting (eg. Docker or VM), but the real interesting one is the Cloud Foundry based solution. It allows for rapid development without worrying about managing the server or the container environment. It takes care of all the mundane bits like setting up basic monitoring system, basic log aggregation, and scaling. You get all those automatically on the web dashboard, or from your command-line if you prefer. Literally all you have to do to deploy webapp, coded in most programming languages, is just one command: 

        cf push YOUR_APP_NAME

  * **Pricing** – This might be more expensive than AWS or Google Compute on paper, but keep in mind this is a PaaS product so that you don’t have to worry about any infrastructure. Bluemix charges for how much memory your app was allocated and for how long. The free tier allows up to 512MB/Hour **per app** – no matter how many instance you choose to deploy. This means you could have 1 app instance with 512MB allocated, or 2 instance of that same app with 256MB allocated, it’s free. You could also have multiple apps, and as long as each app don’t have more than 512MB allocated – it’s still free. But you might think – if I get 8 instance of an app with 64MB allocated each then I’m getting a great deal for 8 instance deployed right? Yes and no, it doesn’t exactly work that way, your CPU allocation is proportional to how much memory you assign to it. So yes you have 8 instance, but each instance has a smaller CPU time slice – meaning you get 8 slow instances of your app.


  * **Support** – Even on the free tier, you can still get reasonable support and raise tickets. The respond time is not the fastest, but usually they do respond within a couple of days. Responding to tickets within a couple of days is better than not responding, but it’s sometimes not good enough if you are paying for usage above the free tier.


  * **SLA** – No where on their site could I find any SLA promises (as of April 2016). They have been running for over a year now, and there are noticeable outages, usually a couple of times a month. Unless you run a hobby project or small enough business to not care, this might a real problem. I do not think this would affect their large enterprise client though who probably have enough negotiation power to get an SLA in place together with a private/dedicated instance. Saying that, I still use it for my random pet projects and NodeRed on Bluemix is simply awesome!