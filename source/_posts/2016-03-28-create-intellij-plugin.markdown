---
layout: post
title: "Tips for creating intellij plugin"
date: 2016-03-28 23:00:00 +0900
comments: false
categories: Java, Kotlin, Intellij Plugin
keywords: Java, Kotlin, Intellij Plugin, Development, develop, implementation
description: IntelliJ Plugin development tips
---

# Summery

There are tips for creating intellij plugins

* Logging
* plugin.xml
* Action

This is note for myself. I won't add description if the sample is clear enough for me.

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

# Logging

Simply use Sys-out

```Java
System.out.println("logging...")
```

However, sys-out shows logs on the console. If you want to show pop up, use notification

## Notification

```Kotlin
Notifications.Bus.notify(Notification("Plugin ID",
                "Title",
                "Content",
                NotificationType.INFORMATION))
```

# plugin.xml

## URL of the plugin

```xml
<idea-plugin url="https://plugins.jetbrains.com/plugin/8262?pr=" version="2">
```

## vendor

```xml
<vendor email="isogai.shiraji@gmail.com" url="https://github.com/shiraji">Shiraji</vendor>
```

## idea-version

To use kotlin, IDE version should be higher than 143.

```xml
<idea-version since-build="143"/>
```

# Action class

## Creating new action

```
right click src > New > action
```

## Methods

```kotlin
override fun actionPerformed(e: AnActionEvent)
```

## Update method

To decide show the action or not, I need to override the update method.

This method should be less than 0.1 sec to complete.

```kotlin
override fun update(e: AnActionEvent?) {
    e ?: return
    super.update(e)

    if (!FindPullRequestModel(e).isEnable()) {
        e.presentation.isEnabled = false
        e.presentation.isVisible = false
    }
}
```

<script async src="//pagead2.googlesyndication.com/pagead/js/adsbygoogle.js"></script>
<!-- 728x90 -->
<ins class="adsbygoogle"
     style="display:inline-block;width:728px;height:90px"
     data-ad-client="ca-pub-3940616565912592"
     data-ad-slot="7693358062"></ins>
<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>
