---
layout: post
title: "Tips for creating intellij plugin"
date: 2016-03-14 01:35:25 +0900
comments: false
categories:
keywords:
description:
---

Notification

```Kotlin
Notifications.Bus.notify(Notification("Plugin Importer+Exporter",
                "Plugin Importer+Exporter",
                "EventData: $foo hash: $currentRevisionHash Annotate: $annotate",
                NotificationType.INFORMATION))
```

Log

Simply

```Java
System.out.println()
```

plugin.xml

URL of the plugin

```xml
<idea-plugin url="https://plugins.jetbrains.com/plugin/8262?pr=" version="2">
```

vendor

```xml
<vendor email="isogai.shiraji@gmail.com" url="https://github.com/shiraji">Shiraji</vendor>
```

To use kotlin

```xml
<idea-version since-build="143"/>
```

Ro create action in right place -> right click src -> New -> action

Action class

Main method

```kotlin
override fun actionPerformed(e: AnActionEvent)
```

update method - should be less than 0.1 sec to complete this method

```kotlin
override fun update(e: AnActionEvent?)
```
