---
layout: post
title: "How to Kontribute (v3)"
date: 2018-03-04 00:00:01 +0900
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

* Setup
* Communication
* 1st Recommended Ways to Kontribute
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

## 1st Recommended Ways to Kontribute

OK, now let's actually work with project. There are 2 recommended ways for 1st commit of Kotlin.

* Adding documentation or comment
* Providing sample codes

### Adding documentations or comment

Adding documentation or comment is one of best ways to start contribute any OSS including Kotlin. My first commit is also [fixing URL of README](https://github.com/JetBrains/kotlin/commit/68dd3dde59b0a339819407cfdf0c465809092e75)

JetBrains says that they want the documentation for standard library. Find a class or a function which are not documented well from https://kotlinlang.org/api/latest/jvm/stdlib/index.html You may think there are not much function that is not documented. Trust me, you can find at least 10 undocumented functions within 1 min!

### Providing sample codes

If you are not good English writer like me, then providing samples are the better way to start contributing Kotlin. https://youtrack.jetbrains.com/issue/KT-20357 is the issue for samples. This issue is especially for new Kontributors.

There are 4 steps to provide sample codes

![samples-steps](https://raw.githubusercontent.com/wiki/shiraji/images/blog/images/how-to-kontribute-v3/how-to-kontribute-v3.028.jpg)

Comment the target API to the issue

![first_comment](https://raw.githubusercontent.com/wiki/shiraji/images/blog/images/how-to-kontribute-v3/first_comment.png)

Then, write sample code.

```kotlin
@Sample
fun groupBy() {
}
```

The method name can be anything but don't add `test` as prefix/postfix because it is not test. Then add sample code inside the method.

```kotlin
@Sample
fun groupBy() {
    val words = listOf("a", "abc", "ab", "def", "abcd")
    val byLength = words.groupBy { it.length }

    assertPrints(byLength.keys, "[1, 3, 2, 4]")
    assertPrints(byLength.values, "[[a], [abc, def], [ab], [abcd]]")

    val mutableByLength: MutableMap<Int, MutableList<String>> = words.groupByTo(mutableMapOf()) { it.length }
    // same content as in byLength map, but the map is mutable
    assertTrue(mutableByLength == byLength)
}
```

There are no hard restriction for writing samples. You can add comment if it is helpful for other developers.

After that, in order to connect with sample code and target method, comment to the target method with `@FQN of the sample method`

```kotlin
/**
...
 * @sample samples.collections.Collections.Transformations.groupBy
 */
public inline fun <T, K> Iterable<T>.groupBy(keySelector: (T) -> K): Map<K, List<T>> {
```

Now, you are ready. Send Pull Request. Then, update you comment with pull request URL

![first_comment2](https://raw.githubusercontent.com/wiki/shiraji/images/blog/images/how-to-kontribute-v3/first_comment2.png)

That's it! Easy! Now, if you want to provide the sample code, please read README for sample code, too.
https://github.com/JetBrains/kotlin/blob/master/libraries/stdlib/samples/ReadMe.md

After the pull request get merged, in some point, you can see your sample code in official documentation http://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/group-by.html

## Developing/Testing Kotlin plugin

This section describes how to develop kotlin plugin features, Intention and Inspection. Intention and Inspection are the two of Frequently Asked Features. These are the short description of Intention and Inspection.

![intention](https://raw.githubusercontent.com/wiki/shiraji/images/blog/images/how-to-kontribute-v3/how-to-kontribute-v3.036.jpg)

![inspection](https://raw.githubusercontent.com/wiki/shiraji/images/blog/images/how-to-kontribute-v3/how-to-kontribute-v3.036-2.jpeg)


