---
layout: post
title: "How to create Intellij custom wizard"
date: 2016-07-11 13:22:11 +0900
comments: false
categories: Intellij, plugin
keywords: Intellij, wizard, plugin, JetBrains
description: Tips of how to create intellij custom wizard
---

# Motivation

When I created Intellij custom wizard called [gradle-intellij-plugin wizard](https://github.com/shiraji/intellij-plugin-with-gradle-wizard),
I read JetBrains [official documentation](http://www.jetbrains.org/intellij/sdk/docs/tutorials/project_wizard.html).
They explains how to [support module types](http://www.jetbrains.org/intellij/sdk/docs/tutorials/project_wizard/module_types.html) and [how to add new steps](http://www.jetbrains.org/intellij/sdk/docs/tutorials/project_wizard/adding_new_steps.html).
However, what I want to know is how to add custom fields, how to add custom files, and how to customize files based on custom fields.

There is no such documentation at this point, so I decided to write this entry for me.

# Topics

* How to add custom view
* How to get the user input in `ModuleWizardStep`
* How to create a file
* How to change the file contents

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

# How to create a file

To create a file for new project, You can use `VfsUtil.saveText(VirtualFile, text)`.

I found really great method in intellij-community repo.

https://github.com/JetBrains/intellij-community/blob/9d6477485d37e796036ee524788231f2f47e6ada/plugins/gradle/src/org/jetbrains/plugins/gradle/service/project/wizard/GradleModuleBuilder.java#L332-L360

I wish they open this method.

# How to change the file contents

Use file template. You can check official documentation of [File and Code Templates](https://www.jetbrains.com/help/idea/2016.1/file-and-code-templates.html)

One thing I want to add is condition. The file template support conditions in following syntax.

```
#if (${LANGUAGE} && ${LANGUAGE} == "Kotlin")
apply plugin: 'kotlin'
#else
apply plugin: 'java'
#end
```

Watch out the indentation. You will generate useless spaces.

I'm not sure but some code formatter does not support `.ft` file. (It actually has a bug adding space before single quotation.)
