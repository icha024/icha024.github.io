---
id: 63
title: Bluemix Config Parser (Java)
date: 2016-01-15T22:55:48+00:00
author: Ian
layout: post
guid: http://www.clianz.com/?p=63
permalink: /2016/01/15/bluemix-config-parser-2/
mytory_md_path:
  - https://raw.githubusercontent.com/icha024/bluemix-config-parser/master/README.md
mytory_md_text:
  - 
mytory_md_mode:
  - url
categories:
  - Bluemix
  - Cloud
  - Java
tags:
  - Bluemix
  - cloud
  - cloudfoundry
  - configuration
  - java
  - json
  - liberty
  - paas
  - parser
---
When using Bluemix (CloudFoundry), services configuration are read by parsing the JSON from VCAP\_SERVICES environment variable. This is a handy util library for parsing Bluemix VCAP\_SERVICES configurations JSON. Common configs classess are provided so it’ll even work with auto-completion on your favorite IDE. No more messy JSON config parsing!
<!--more-->

## Usage example

```java
BluemixConfigStore.getConfig().getCloudantNoSQLDB().getCredentials().getPassword();
```

## Installing with Maven

```xml
<dependency>
  <groupId>com.clianz</groupId>
  <artifactId>bluemix-config-parser</artifactId>
  <version>0.0.5</version>
</dependency>
```

## Configurations supported

Most of the common services on Bluemix are currently supported:

- Business rules
- Clear DB
- Cloud AMQP (RabbitMQ)
- Cloudant
- Concept Expansion
- Concept Insight
- Dash DB
- Data Cache
- Document Conversion
- Geospartial Analytics
- IMF Push
- Monitoring and Analytics
- MQ Light
- Relationship Extraction
- Rank and Retrieve
- Sesson Cache
- SQL DB (DB2)
- Trade-off Analytics
- Twitter Insights
- Weather Insights
- Workflow
- Workload Scheduler
- Twilio
- Elastic Search by Compose
- Mongo DB by Compose
- PostgreSQL by Compose
- Redis by Compose

Add more bindings to other services if you need, let’s do this once and for all for everyone.

## Local development deployment

For local development, there are two common ways to setup environment:
  
* Set VCAP_SERVICES JSON config for your dev space as an environment variable.
  
* Or, start your app with a JVM variable pointing to a file containing your dev config, eg.

```properties
-DVCAP_SERVICES_FILENAME=myDevConfig.json
```

## Contributing

Pull requests are welcome.
  
Project page: <https://github.com/icha024/bluemix-config-parser/>

Just fork this project, follow the pattern to add bindings/tests, then raise a pull request.