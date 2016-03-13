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
