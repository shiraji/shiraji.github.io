---
layout: post
title: "How to Kontribute (v3)"
date: 2018-03-04 00:00:01 +0900
comments: false
categories: Kotlin OSS GitHub 
keywords: Kotlin Kontribute Kontributor
description: Describe how to contribute Kotlin in 2018
---

This blog post describes how to start Kontribute.

## Who am I?

![intro](https://raw.githubusercontent.com/wiki/shiraji/images/blog/images/how-to-kontribute-v3/how-to-kontribute-v3.002.jpg)

I've been kontributors since July 2016. I made more than 60 commits by now. I love Kotlin, my beautiful wife and son.

I talked about this topic a few times in many places. After speaking at DroidKaigi 2018 and Kotlin Night Kolkata, I thought making blog post in English is also helpful for new Kontributors. I am too lazy to write this from scratch, so I re-used some of slides from those presentation.

## Prerequisite

![Prerequisite](https://raw.githubusercontent.com/wiki/shiraji/images/blog/images/how-to-kontribute-v3/how-to-kontribute-v3.004.jpg)

This blog entry does not explain how to use git/github and how to write Kotlin.

## Outline

* Setup
* Communication
* Recommended Ways to Kontribute
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

If you cannot build or run the project, ask other kontributors! In next section, I will talk about how to intract with other Kontributors or JetBrains team.

## Communication

Communication is one of the key success factor for Kontribution.

I will introduce 3 tools that Kontributors usually use.

### Slack

<img src="https://raw.githubusercontent.com/wiki/shiraji/images/blog/images/how-to-kontribute-v3/slack_rgb.png" width="250" style="border: none;" />

Slack is one of most important communication tool for Kontributors. In case you are not join `Kotlinlang` team, get the invitation from http://slack.kotlinlang.org

There is public channel `kontributors`. This is the one that most of contributors and JetBrains stuff are staying.

If you have trouble building Kotlin project or anything that is related to Kontribution, ask those questions at this channel.

Normally, people respond within one business day.

### YouTrack

<img src="https://raw.githubusercontent.com/wiki/shiraji/images/blog/images/how-to-kontribute-v3/logo-text.png" width="250" style="border: none; padding: 16px" />

The second one is [YouTrack](https://www.jetbrains.com/youtrack/). YouTrack is the issue management tool that JetBrains developed. 

Kotlin's public issues are listed https://youtrack.jetbrains.com/issues/KT

You can work any issues if there is no assignees or no progress. 

In case you are not sure which issue you want to work with, check for `up-for-grabs` tag or https://youtrack.jetbrains.com/issues/KT?q=tag:%20%7BUp%20For%20Grabs%7D%20%23Unresolved These issues are free to contribute.

After you find the issue you want to work with, the first thing you should do is comment "I'm going to do this". Since we are external contributors, JetBrains staff are not able to assign our name as assignee. However, once you comment the issue, you are treated as assignee.

### GitHub

<img src="https://raw.githubusercontent.com/wiki/shiraji/images/blog/images/how-to-kontribute-v3/GitHub_Logo.png" width="250" style="border: none;" />

The last one is GitHub. GitHub is where JetBrains staff give us a feedback of your pull request. GitHub are not used for a lot of communication, except [KEEP](https://github.com/Kotlin/KEEP).

After sending pull request, I recommend to write pull request URL to YouTrack's issue. This is because JetBrains staff don't look at GitHub. They constantly check YouTrack but not GitHub. If you comment URL to the issue, the issue goes to top of the list. So, they can easily notice there is some progress for the issue.

