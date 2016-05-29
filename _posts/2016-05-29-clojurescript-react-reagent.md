---
id: 127
title: Reagent - ClojureScript and React made easy 
date: 2016-05-27T22:55:48+00:00
author: Ian
layout: post
permalink: /2016/05/27/clojurescript-react-reagent/
mytory_md_mode:
  - url
categories:
  - Clojure
  - ClojureScript
  - React
  - Reagent
tags:
  - Clojure
  - ClojureScript
  - React
  - Reagent
---
Recently I've started reading into Clojure and came across something really interesting.

Clojure is a JVM based functional programming language, based on Lisp. React is a recent web frontend framework from Facebook that has been everywhere lately. React a reactive Javascript framework, similar to AngularJS, but takes a different approach in component management to make it much faster and more scalable.

Now this is where it gets interesting - Clojure can be compiled to Javascript via the ClojureScript plugin. Combine the two and you get Reagent, a framework that integrated them together nicely.
<!--more-->

## Starting a Reagent Project - in 3 steps

There are two ways to run ClojureScript + React, the first to build multi page app and build both server and client side in Clojure, the other way is to just build the frontend with it as a single page app. We're much more interested in the latter.

1. **First download and install [Leiningen](http://leiningen.org/)** for building Clojure. Once installed, we can **generate the project** a new Reagent frontend project with this easily by running:

```
lein new reagent-frontend myproject
```
2. **To run it**:

```
cd myproject
lein cljsbuild auto
```
3. **Open the html file** with your browser

```
myproject/public/index.html
```
You should see the 'Welcome to Reagent' demo page. Simple!

## How did it work?

The Clojure script in

```
myproject/src/myproject/core.cljs

```
got compiled into a Javascript file at

```
myproject/public/js/app.js
```

The index.html page does nothing more than including that Javascript file, and voila!

## Making things happen reactively
Now let's have a look at the reactive part by demoing the obligatory live counter button. Update your core.cljs file to the following:

```clj
(ns myproject.core
    (:require [reagent.core :as r :refer [atom]]
              [reagent.session :as session]
              [secretary.core :as secretary :include-macros true]))

;; -------------------------
;; Views

(defonce click-count (r/atom 0))

(defn home-page []
  [:div
    [:h2 "Welcome to Reagent"]
    [:p "Just playin around..."]
    [:input {:type "button" :value "click this" :on-click #(swap! click-count inc)}]
    [:p "i've been clicked for: " @click-count]
  ]
)

;; -------------------------
;; Initialize app

(defn mount-root []
  (r/render [home-page] (.getElementById js/document "app")))

(defn init! []
  (mount-root))
```

Refresh the index.html page and you will see the button with a counter below to increment count on click.

[Demo code available on Github](https://github.com/icha024/clojurescript-react-playground/tree/freeze)

## More Reading
- Reactive framework from Facebook: https://facebook.github.io/react/
- Reagent framework: https://reagent-project.github.io/

That's all for now, I got to go hone up my Clojure skills!
