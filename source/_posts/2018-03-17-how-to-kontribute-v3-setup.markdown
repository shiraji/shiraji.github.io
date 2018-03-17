---
layout: post
title: "How to Kontribute (v3) - Setup"
date: 2018-03-17 00:00:01 +0900
comments: false
categories: Kotlin OSS GitHub
keywords: Kotlin Kontribute Kontributor
description: Describe how to contribute Kotlin in 2018
---

This blog post describes how to start Kontribute or how to start contributing [Kotlin](https://kotlinlang.org/).

<script async src="//pagead2.googlesyndication.com/pagead/js/adsbygoogle.js"></script>
<!-- 728x90 -->
<ins class="adsbygoogle"
     style="display:inline-block;width:728px;height:90px"
     data-ad-client="ca-pub-3940616565912592"
     data-ad-slot="7693358062"></ins>
<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

<!-- more -->

## Who am I?

![intro](https://raw.githubusercontent.com/wiki/shiraji/images/blog/images/how-to-kontribute-v3/how-to-kontribute-v3.002.jpg)

I've been kontributors since July 2016. I made more than 60 commits by now. I love Kotlin, my beautiful wife and lovely son.

I talked about this topic a few times in different places. After speaking at DroidKaigi 2018 and Kotlin Night Kolkata, some people gave me feed back that it is also helpful for new Kontributors if I make a blog post in English. I am too lazy to write this from scratch, so I re-used some of contents from those presentation.

## Prerequisite

![Prerequisite](https://raw.githubusercontent.com/wiki/shiraji/images/blog/images/how-to-kontribute-v3/how-to-kontribute-v3.004.jpg)

This blog entry does not explain how to use git/github and how to write Kotlin.

Since I mainly contribute to Kotlin plguin features, this blog post focus on how to contribute kotlin plugin. However, once you undersntand how to do it, it is really easy to contribute to Kotlin Languages, Kotlin JS or Kotlin Native because it is in the same repository.

## Outline

* [Setup](http://shiraji.github.io/blog/2018/03/17/how-to-kontribute-v3-setup/)
* [Communication](http://shiraji.github.io/blog/2018/03/17/how-to-kontribute-v3-communication/)
* [1st Recommended Ways to Kontribute](http://shiraji.github.io/blog/2018/03/17/how-to-kontribute-v3-recommend/)
* Developing/Testing Kotlin plugin

## Setup

It "was" the hardest part but not anymore. This is the reason I updated this topic as v3. There are "only" 4 parts of setups

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

As for Intellij IDEA, you can use both community and ultimate version.

To make sure you are using latest kotlin plugin, check the following gif.

![plugin_up-to-date](https://raw.githubusercontent.com/wiki/shiraji/images/blog/images/how-to-kontribute-v3/plugin_up-to-date.gif)

### Open project

After setup Intellij IDEA, you can open kotlin plugin. First time you open the project, Intellij downloads all dependencies using gradle. It took me about 50 mins. Just wait.

### Module

After downloading dependencies, you will notice that there is no source code in Project view. To fix this problem, you will need to do the followings:

* Go to File | New | Module from Existing Sources | and then select
`build.gradle.kts` file
* After selecting the file, `Import Module from Gradle` dialog popup. Select `use default gradle wrapper`

The following gif visually describes the steps

![module](https://raw.githubusercontent.com/wiki/shiraji/images/blog/images/how-to-kontribute-v3/module.gif)

### Run

Setup is done. Let's run it. To run it, select `IDEA` run configuration and hit run button.

![run](https://raw.githubusercontent.com/wiki/shiraji/images/blog/images/how-to-kontribute-v3/run.gif)

After waiting a few minites, the child IDEA shows up. What does this do is if you change your Kotlin project, the change is bundle to this child IDEA. You can test your new feature in this child IDEA.

If you cannot build or run the project, ask other kontributors! In next blog post, I will talk about how to intract with other Kontributors or JetBrains team.
