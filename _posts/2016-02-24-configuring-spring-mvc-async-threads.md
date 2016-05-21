---
id: 98
title: Configuring Spring MVC Async Threads
date: 2016-02-24T14:20:52+00:00
author: Ian
layout: post
guid: http://www.clianz.com/?p=98
permalink: /2016/02/24/configuring-spring-mvc-async-threads/
mytory_md_path:
  - 
mytory_md_text:
  - 
mytory_md_mode:
  - url
categories:
  - Java
  - Spring
  - Spring-boot
tags:
  - async
  - AsyncTaskExecutor
  - callable
  - configuration
  - Spring MVC
  - thread
---
### Spring MVC Aync – Awesome!

The great thing I love about Spring web framework is the abundance of features and flexibility. The ‘callable’ async web request handler that came with <a href="https://spring.io/blog/2012/05/10/spring-mvc-3-2-preview-making-a-controller-method-asynchronous/" target="_blank">Spring MVC 3.2</a> is one of them. Basically we can return a ‘callable’ wrapping the type you’d normally use – and it’ll auto-magically turn it into an async web call.

Here is an example of it (Spring Boot + Java 8), this just echoes the request in the response:

```java
@RequestMapping(value = "/echo")
public Callable<ResponseEntity<String>> echo(@RequestParam(value = "message", required = true) String message) {
	return () -> {
		return new ResponseEntity<>(message, HttpStatus.OK);
	};
}
```

Now if you deploy this locally, then <http://localhost:8080/echo?message=auckland> will echo your request in the response – all done asynchronously on the server.

### But it’s not quite plug & play…

One of the things to watch out for is that the default thread executor should be replaced for production. However, I could not find any good example of this easily. After digging around the code for a while, here is an example of how to configure the async task executor.

```java
/**
	* Configs for MVC/REST services with 'Callable' return types - they spawn background threads, and this configures it.
	*/
@Configuration
@ConfigurationProperties(prefix = "web.executor")
public class CallableConfig {

	public static final int DEFAULT_POOL_SIZE = 2;
	public static final int DEFAULT_QUEUE_SIZE = 2;

	private int minPoolSize = DEFAULT_POOL_SIZE;
	private int maxPoolSize = DEFAULT_POOL_SIZE;
	private int maxQueueSize = DEFAULT_QUEUE_SIZE;

	@Bean
	public AsyncTaskExecutor asyncTaskExecutor() {
		final ThreadPoolTaskExecutor executor = new ThreadPoolTaskExecutor();
		executor.setCorePoolSize(DEFAULT_POOL_SIZE);
		executor.setMaxPoolSize(DEFAULT_POOL_SIZE);
		executor.setQueueCapacity(DEFAULT_QUEUE_SIZE);
		executor.setRejectedExecutionHandler(new ThreadPoolExecutor.AbortPolicy());
		executor.setWaitForTasksToCompleteOnShutdown(true);
		return executor;
	}

	@Bean
	public WebMvcConfigurerAdapter webMvcConfigurerAdapter(AsyncTaskExecutor asyncTaskExecutor) {
		return new WebMvcConfigurerAdapter() {
			@Override
			public void configureAsyncSupport(AsyncSupportConfigurer configurer) {
				configurer.setTaskExecutor(asyncTaskExecutor);
				super.configureAsyncSupport(configurer);
			}
		};
	}
}    
```

### Exposing Configurations

The @ConfigurationProperties tag exposes the configuration if you are using Spring Boot. This handy option let you configure it in your application.properties to override the defaults, for example:

```properties
web.executor.minPoolSize=10
web.executor.maxPoolSize=10
web.executor.maxQueueSize=20
```