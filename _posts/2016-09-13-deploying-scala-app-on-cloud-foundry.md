---
id: 130
title: Deploying Scala/Spray App on Cloud Foundry
date: 2016-09-13T08:21:49+00:00
author: Ian
layout: post
permalink: /2016/09/13/deploying-scala-app-on-cloud-foundry/
mytory_md_path:
  - 
mytory_md_text:
  - 
mytory_md_mode:
  - url
categories:
  - Bluemix
  - Cloud
  - Scala
---

A few months ago I was playing with Scala/Spray on Bluemix (Cloud Foundry). Deploying it wasn't as straight forward as I thought, here is what I learnt. 
<!--more-->

## Building the Jar

Creating a straight jar artefact with Maven shade plugin run perfectly on local machine. However, this artefact doesn't seem to work on Bluemix when deployed.

This is strange because Cloud Foundry/Bluemix should support all standard JVM executable. A quick look at the [buildpack](https://github.com/cloudfoundry/java-buildpack) seem to suggest it does support a variety of artefact formats, so let's go to plan B...

## What worked
One of the other artefact formats it support is the [distzip](https://github.com/cloudfoundry/java-buildpack/blob/master/docs/container-dist_zip.md) format. Once the build script was switched to generate artefact in that format, it worked!

Using the distzip format should be straight forward if you are using Gradle build system. If you prefer Maven, then check out [this build file](https://github.com/icha024/scala-forex/blob/master/pom.xml) in my demo project.

Once built, all that is left is to push the zip:
```
cf push awesomeServiceName -p awesome-projet-1.0-SNAPSHOT-bin.zip
```

I haven't looked back at why the basic Jar didn't work, it's something still on my ToDo list. Hope this helps if someone else ran into this problem.