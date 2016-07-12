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

When I created Intellij custom wizard called [gradle-intellij-plugin wizard](https://github.com/shiraji/intellij-plugin-with-gradle-wizard),
I read JetBrains [official documentation](http://www.jetbrains.org/intellij/sdk/docs/tutorials/project_wizard.html).
They explains how to [support module types](http://www.jetbrains.org/intellij/sdk/docs/tutorials/project_wizard/module_types.html) and [how to add new steps](http://www.jetbrains.org/intellij/sdk/docs/tutorials/project_wizard/adding_new_steps.html).
However, what I want to know is how to add custom fields, how to add custom files, and how to customize files based on custom fields.

There is no such documentation at this point, so I decided to write this entry

# Target reader

* Understand how to setup intellij plugin project
* Completed http://www.jetbrains.org/intellij/sdk/docs/tutorials/project_wizard.html

# Topics

* How to add custom view
* How to create a file
* How to get the user input in `ModuleWizardStep`
* How to change file contents

# How to add custom fields

To add custom views, it really simple.

```java
@Override
public JComponent getComponent() {
    return new JLabel("Provide some setting here");
}
```

Add custom views to `JComponent` and return `getComponent()`.

If you make a form file which binds to your `ModuleWizardStep`, then just return root panel.

In my case, I created GUI [form file](https://github.com/shiraji/intellij-plugin-with-gradle-wizard/blob/1.0.1/src/main/kotlin/com/github/shiraji/ipgwizard/step/IPGWizardSupportLanguageStep.form)
Then, bind the form file to [IPGWizardSupportLanguageStep](https://github.com/shiraji/intellij-plugin-with-gradle-wizard/blob/1.0.1/src/main/kotlin/com/github/shiraji/ipgwizard/step/IPGWizardSupportLanguageStep.java).

It ends up

![language](https://raw.githubusercontent.com/shiraji/intellij-plugin-with-gradle-wizard/master/website/images/language.png)

In order to add "Project SDK" field, you need to override `getModuleType()` in your `ModuleBuilder`

For instance, to pick standard Java sdk, write following code in kotlin.

```kotlin
override fun getModuleType() = StdModuleTypes.JAVA
```

# How to get the user input in `ModuleWizardStep`

If a user click "Next" button, `ModuleWizardStep#updateDataModel()` will be called. You should pass/save the user input in the method.

In my case, it looks really bad design, but it directly passes the language to builder.

https://github.com/shiraji/intellij-plugin-with-gradle-wizard/blob/1.0.1/src/main/kotlin/com/github/shiraji/ipgwizard/step/IPGWizardSupportLanguageStep.java#L43-L46

```java
@Override
public void updateDataModel() {
    builder.setLanguage(languages.get(languageComboBox.getSelectedIndex()));
}
```

# How to change file contents

To create a file for new project, You can use `VfsUtil.saveText`


# How to create a template file

Using a template file is the best way to customize new files.

You need to create template files in `resources/fileTemplates/internal` (or `src/main/resources/fileTemplates/internal` in gradle project)

The file extension is `.ft`. The file name should be unique.

For instance, if you want to add `.gitignore` file, create `unique_name_.gitignore.ft` with following contents

```
.gradle
/local.properties
/.idea/workspace.xml
/.idea/libraries
/.idea/dictionaries
.DS_Store
/build
```

After below section, you will be able to create `.gitignore` file.







# トピック

* カスタムフィールドの作成方法
* ファイルの追加方法
* テンプレートファイルのカスタマイズ方法
