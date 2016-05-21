---
id: 111
title: Cake Pattern and Magnet Pattern in Scala
date: 2016-04-26T08:46:20+00:00
author: Ian
layout: post
guid: http://www.clianz.com/?p=111
permalink: /2016/04/26/scala-cake-pattern-and-magnet-pattern/
mytory_md_path:
  - 
mytory_md_text:
  - 
mytory_md_mode:
  - url
categories:
  - Scala
tags:
  - Cake pattern
  - Magnet pattern
  - Scala
---
Last week I <a href="https://github.com/icha024/HelloScala/tree/master2/HelloScala/src/main/scala/com/github/icha024" target="_blank">started looking into Scala</a>, it’s one of those things that has been on my to-do list for a very long time and but never got to it until now. Looking back, I should have bumped it up the list a long time ago.

A couple of interesting patterns I’ve came across was the Cake and the Magnet pattern. I usually find it useful to relate new things to my old understanding in other technology stack, and being primarily a Java developer, here is a my short explanation with Java.

## Cake Pattern

This ‘Cake Pattern’ is a random name (IMO) of what Java developer normally called ‘best practice dependency injection (DI)’ with Spring. Many of you here have probably been doing this for a very long time already in the Java/Spring world. Basically it means ‘one should only inject via interface, not concrete class’.

### Dependency injection

Imagine if I have a service that needs to do some data operation, normally we’d create a ‘Service’ class with a ‘DAO’ member – where ‘DAO’ is an interface. At runtime, we’d inject what DAO implementation we want, eg. SqlDao or CloudantDao for example.

    MyService –(uses)–> GenericDAO interface**

Here in Scala a ‘trait’ act like a plain old Java interface or contain implementation (like Java 8 ‘default’ method on interface), so we use it to declare both interface and implementation:

    SqlDao –(implements)–> GenericDAO interface

    CloudantDao –(implements)–> GenericDAO interface

Then at when you need to run the code, just inject whichever implementation you want into the service
  
    MyService –(uses)–> GenericDAO interface (with SqlDao implementation injected) 
  
_or_ 
  
    MyService –(uses)–> GenericDAO interface (with CloudantDao implementation injected) 

### Scala Cake Pattern Example

(<a href="https://github.com/icha024/HelloScala/blob/master2/HelloScala/src/main/scala/com/github/icha024/MyCake.scala" target="_blank">github</a>)
  
Here is a little demo program to show this DI concept.

```scala
package com.example

object MyCake extends App {

  // This is an 'interface'
  trait GenericDao {
    def Imp: String
  }

  // This is like implementing interface with default in Java8 (An implementation)
  trait SqlDao extends GenericDao { 
    def Imp: String = "SQL Implementation"
  }

  // This is like implementing interface with default in Java8 (An implementation)
  trait CloudantDao extends GenericDao {

    def Imp: String = "Cloudant Implementation"
  }

  trait MyServiceTrait {
    // This relies on 'interface' instead of 'implementation' - it just normal Java/Spring best practice anyway.
    dao: GenericDao => // Syntax for trait 'self type annotation', we really should just call it 'extending an interface'
    println("My implementation is: " + dao.Imp)
  }

  val svc = new MyServiceTrait with CloudantDao  // DI with Cloudant Impl
  println("svc using Cloudant DI returns: " + svc.Imp)

  val svc2 = new MyServiceTrait with SqlDao  // DI with SQL Impl
  println("svc2 using SQL DI returns: " + svc2.Imp)

}
```

**_Update:_ But wait – self type annotation is more like ‘inheritance’ than ‘composition’**

After having a chat with a colleague – notice in the example above there is:

    dao: GenericDao => 

In Java/Spring, DI is usually composition – but here we are doing DI via traits and the self type annotation is more like inheriting. Normally, in most example I saw, it’s named ‘this’ instead of ‘dao’ – I guess that make more sense if we are inheriting (injecting) multiple traits. See the updated example below multiple traits:

```scala
package com.example

object MyCake extends App {

  // shared

  trait AnotherTrait {
    // Some method
  }

  trait GenericDao { // This is an 'interface'
    def Imp: String
  }

  trait SqlDao extends GenericDao { // This is like implementing interface with default in Java8 (An implementation)
    def Imp: String = "SQL Implementation"
  }

  trait CloudantDao extends GenericDao { // This is like implementing interface with default in Java8 (An implementation)
    def Imp: String = "Cloudant Implementation"
  }

  trait MyServiceTrait {
    // This relies on 'interface' instead of 'implementation' - it just normal Java/Spring best practice anyway.
    this: GenericDao with AnotherTrait => // Syntax for trait 'self type annotation', we really should just call it 'extending an interface'
    println("My implementation is: " + this.Imp)
  }


  // service customisation (inject stuff here)

  val svc = new MyServiceTrait with CloudantDao with AnotherTrait
  println("svc using Cloudant DI returns: " + svc.Imp)

  val svc2 = new MyServiceTrait with SqlDao with AnotherTrait
  println("svc2 using SQL DI returns: " + svc2.Imp)

}
```

## Magnet Pattern

You are probably familiar with method overloading (ie. multiple method with the same name, but different parameter). This is very popular in Java, but is still not available in some modern languages like GoLang because designer to argue it clutters up the language.

What do you do if you want a method that do different things with different parameter without overloading method? Simple answer is to pass in a single ‘object’ that encapsulate all the parameters and optional stuff you want. This is the Magnet pattern in a nutshell.

### The Implicit Magnet

One of the nice features of Scala is ‘implicit’ method. Here you can define a method with ‘implicit’ tag, and when you didn’t call a method with the exact signature, the compiler will auto-magically find the best matching method to use to fill in the missing gap (implicitly).

### Scala Magnet Pattern Example

(<a href="https://github.com/icha024/HelloScala/blob/master2/HelloScala/src/main/scala/com/github/icha024/MyMagnetChecker.scala" target="_blank">github</a>)
  
Here is a little demo program to show this Magnet pattern.

Notice how MyMagnet is declared – I did not provide any ‘String’ or ‘Int’ type constructor. Scala is smart enough to find the matching implicit method for ‘String’ and ‘Int’ in my object and still construct it properly.

```scala
package com.example

object MyMagnetChecker extends App {
  trait MyMagnet { // should probably be sealed
    type Result
    def getRes: Result
    def apply(): Result
  }

  object MyMagnet {
    implicit def convertFromInt(myVal: Int) = new MyMagnet {
      override type Result = Int
      override def apply(): Result = myVal
      override def getRes: Result = myVal
    }

    implicit def convertFromString(myVal: String) = new MyMagnet {
      override type Result = String
      override def apply(): Result = "Type is a String: " + myVal
      override def getRes: Result = "String type: " + myVal
    }

    implicit def convertFromTwoString(myTuple: (String, String)) = new MyMagnet {
      override type Result = String
      override def apply(): Result = "Type is a String type 2: " + myTuple._1 + ", " + myTuple._2
      override def getRes: Result = "String2 type: " + myTuple._1 + ", " + myTuple._2
    }
  }

  def findStickyType(mag: MyMagnet): Unit = {
    println("Finding magnet of some type... " + mag.getRes)
  }

  findStickyType("abc"); // create 'MyMagnet' with String constructor
  findStickyType(123);   // create 'MyMagnet' with Int constructor
  findStickyType("xyz","ijk");   // create 'MyMagnet' with (String, String) tuple constructor

  // Magnet pattern = Instead of method overloading, we use one big pojo with variable amount of fields.
  // Then instead of messy if/else checks on each field of the big pojo to run different init, we use 'implicit' method feature of Scala.
}
```