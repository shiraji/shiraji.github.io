---
layout: post
title: "How to create Intellij custom wizard"
date: 2016-07-11 13:22:11 +0900
comments: false
categories: Intellij, plugin
keywords: Intellij, wizard, plugin, JetBrains
description: Describe how to create intellij custom wizard (with more example compare with official documentation)
---

Japanese text is following

# Motivation

When I created Intellij custom wizard,
I first read JetBrains [official documentation](http://www.jetbrains.org/intellij/sdk/docs/tutorials/project_wizard.html).
They explains how to [support module types](http://www.jetbrains.org/intellij/sdk/docs/tutorials/project_wizard/module_types.html) and [how to add new steps](http://www.jetbrains.org/intellij/sdk/docs/tutorials/project_wizard/adding_new_steps.html).
However, what I want to know is how to add custom fields, how to add custom files, how to customize files based on custom fields, and so on.

There is no such documentation at this point

# Topics

* How to setup plugin project
* How to add custom fields
* How to add new files (template)
* How to customize files based on fields

# How to setup plugin project

**If you don't like gradle project, please skip this section.**

Since I love [gradle-intellij-plugin](https://github.com/JetBrains/gradle-intellij-plugin), let's create plugin project using Gradle.
Please check official documentation of [Building plugins with Gradle](http://www.jetbrains.org/intellij/sdk/docs/tutorials/build_system.html)

You can also use my [gradle-intellij-plugin wizard](https://github.com/shiraji/intellij-plugin-with-gradle-wizard) to skip copy and paste gradle related files.


