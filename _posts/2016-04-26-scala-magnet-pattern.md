---
id: 111
title: Magnet Pattern in Scala
date: 2016-04-26T08:46:21+00:00
author: Ian
layout: post
guid: http://www.clianz.com/?p=111
permalink: /2016/04/26/scala-magnet-pattern/
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
Continuing from the previous post on Scala's Cake Pattern, here is a quick explaination of the Magnet pattern.

## Magnet Pattern
You are probably familiar with method overloading (ie. multiple method with the same name, but different parameter). This is very popular in Java, but is still not available in some modern languages like GoLang because designer to argue it clutters up the language.
<!--more-->

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