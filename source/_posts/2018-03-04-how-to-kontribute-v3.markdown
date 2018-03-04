---
layout: post
title: "How to Kontribute v3"
date: 2018-03-04 00:00:01 +0900
comments: false
categories: Kotlin OSS GitHub 
keywords: Kotlin Kontribute Kontributor
description: Describe how to contribute Kotlin in 2018
---

This blog post describes how to start Kontribute.

## Who am I?

After speaking at KotlinConf 2017, Kotlin Night Kolkata, and DroidKaigi 2018, I thought making blog post in English is also helpful for new Kontributors. I am too lazy to write this from scratch, so I re-used some of slides from those presentation.

![intro](https://raw.githubusercontent.com/wiki/shiraji/images/blog/images/how-to-kontribute-v3/how-to-kontribute-v3.002.jpg)

I've been kontributors since July 2016. I made more than 60 commits by now. I love Kotlin, my beautiful wife and son.

## Prerequisite

![Prerequisite](https://raw.githubusercontent.com/wiki/shiraji/images/blog/images/how-to-kontribute-v3/how-to-kontribute-v3.004.jpg)

This blog entry does not explain how to use git/github and how to write Kotlin.

## Outline

* Setup
* Communication
* Recommended Ways to Kontribute
* Developing/Testing Kotlin plugin

## Setup

It "was" the hardest part but not anymore. There are "only" 4 parts of setups

### JDK

The first one is JDK.

![JDK](https://raw.githubusercontent.com/wiki/shiraji/images/blog/images/how-to-kontribute-v3/how-to-kontribute-v3.007.jpg)

You need to install 3 differenct versions of JDKs.

In order to use those JDK, you must setup environment variables like this

![JDK-env-var](https://raw.githubusercontent.com/wiki/shiraji/images/blog/images/how-to-kontribute-v3/how-to-kontribute-v3.008.jpg)

It's quite annoying to set each environment variables. Here is what I did.

```
export JAVA_HOME=`/usr/libexec/java_home -v "1.8"`
export JDK_16=`/usr/libexec/java_home -v "1.6"`
export JDK_17=`/usr/libexec/java_home -v "1.7"`
export JDK_18=`/usr/libexec/java_home -v "1.8"`
```

### Intellij IDEA & Plugins

![JDK-env-var](https://raw.githubusercontent.com/wiki/shiraji/images/blog/images/how-to-kontribute-v3/how-to-kontribute-v3.008.jpg)

To make sure you are using latest kotlin plugin, check the following movie.

![plugin_up-to-date](https://raw.githubusercontent.com/wiki/shiraji/images/blog/images/how-to-kontribute-v3/plugin_up-to-date.gif)

