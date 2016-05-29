---
id: 128
title: Generating Java Spring-MVC code from Swagger Spec
date: 2016-05-29T10:43:48+00:00
author: Ian
layout: post
permalink: /2016/05/29/java-mvc-swagger-gen/
mytory_md_path:
  - 
mytory_md_text:
  - 
mytory_md_mode:
  - url
categories:
  - Java
  - Spring
  - Swagger
tags:
  - java
  - swagger
  - callable
  - code-gen
  - Spring MVC
---
REST services skeleton code generation is a quick way to get started in implementing the service from popular Swagger spec describing the interface.

In this post we'll look at generating Java Spring MVC code from Swagger spec. There are a few frameworks out there, but at the time I couldn't find the one that I want - and so I've created a new template for my needs. Here is a quick walkthrough on setting up code-gen for Java Spring MVC project.
<!--more-->

## Spring MVC Java Templates
There are plenty of projects and templates out there there to generate skeleton server code from Swagger spec, but at the time I haven't came across one that suit my needs. Ideally it should:

- Take advantage of Java **servlet 3.0+ async** feature.

- Generate code for **Spring MVC** (my preferred framework)

- Integrate with the rest of my stack and build tools (**Spring Boot + Maven**)

- Generate **code directly in target/gen**, instead of a new standalone stub that need us to manually copy/paste the bits we want.
 This will be useful for updating Swagger spec on existing project, otherwise it's messy trying to manually figure out what is fresh generated code and merge it with existing implementations.

## The Solution

### Introducing the j8-async template in Swagger-codegen

The default Spring MVC template from Swagger codegen was a good start. The **j8-async** template is a modified version of it with the following changes:

- Added ```<Callable>``` tag to response to use Spring's async servlet feature.

- Generate code with Java 8's inteface default instead of a plain class.

This new template was [merged into swagger-codegen version 2.1.5](https://github.com/swagger-api/swagger-codegen/pull/1742), you may choose to generate code with it by passing in 'j8-async' instead of the 'default'

```xml
<!-- Swagger codegen -->
<dependency>
	<groupId>io.swagger</groupId>
	<artifactId>swagger-core</artifactId>
	<version>${swagger.version}</version>
</dependency>
```

We'll also need Spring-Fox dependency for codegen:

```xml
<dependency>
	<groupId>io.springfox</groupId>
	<artifactId>springfox-swagger2</artifactId>
	<version>${springfox.version}</version>
</dependency>
```

And the associated config in swagger-properties for api-docs:

```properties
springfox.documentation.swagger.v2.path=/api-docs
```

### Combining with Maven plugin
Normally the swagger-code gen project just generates stub, this is now what we want. There is a Maven plugin to get around this to generate code into our target/gen - combine this with the Java 8 interface default feature, and we have a generate stub with default implementation nicely integrated with your project.

```xml
<!-- Generate REST default interface from Swagger spec -->
<plugin>
	<groupId>io.swagger</groupId>
	<artifactId>swagger-codegen-maven-plugin</artifactId>
	<version>${swagger.codegen.version}</version>
	<executions>
		<execution>
			<goals>
				<goal>generate</goal>
			</goals>
			<configuration>
				<inputSpec>src/main/resources/swagger.yaml</inputSpec>
				<language>spring-mvc</language>
				<configOptions>
					<sourceFolder>src/gen/java/main</sourceFolder>
					<library>j8-async</library>
				</configOptions>
			</configuration>
		</execution>
	</executions>
</plugin>
```

Just point it to your swagger spec and pick the 'j8-async' option. Because it uses a default interface, it will already work out of the box with the stub response.

## Overriding the stub
To provide a custom implementation on the operation, just implemented the gerated API then override the stub method.

For example, here we generate Java stub for the Uber Swagger spec, then override the '/me' operation by overriding the generated 'MeApi' interface:

```java
@RestController
public class MeService implements MeApi {

	// Tip: In Intellj, use alt-insert to select which operation to override.
	@Override
	public Callable<ResponseEntity<Profile>> meGet() throws NotFoundException {
		return () -> {
			Profile profile = new Profile();
			profile.setFirstName("Ian");
			return new ResponseEntity<>(profile, HttpStatus.OK);
		};
	}
}
```

We can pick which operations to override and customise easily as the swagger spec doc changes, no more manually copy/pasting from the stub!

## Sample project on Github
The sample project can be found at: [https://github.com/icha024/java-mvc-swagger-gen-demo](https://github.com/icha024/java-mvc-swagger-gen-demo)

This uses the Uber Swagger spec as mentioned above, and override only the 'Me' API with custom inplementation.

Happy Swag Coding!