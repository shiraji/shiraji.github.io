---
layout: post
title: "How to create Intellij custom wizard"
date: 2016-07-11 13:22:11 +0900
comments: false
categories: Intellij, plugin
keywords: Intellij, wizard, plugin, JetBrains
description: Tips of how to create intellij custom wizard
---

Japanese text is following.(日本語は英語の下です。)

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

---

日本語

# モチベーション

Intellijのカスタムwizard [gradle-intellij-plugin wizard](https://github.com/shiraji/intellij-plugin-with-gradle-wizard)を作った時、JetBrainsの[公式ドキュメント](http://www.jetbrains.org/intellij/sdk/docs/tutorials/project_wizard.html)を読みました。

ただ、この公式ドキュメントは[support module types](http://www.jetbrains.org/intellij/sdk/docs/tutorials/project_wizard/module_types.html)と[how to add new steps](http://www.jetbrains.org/intellij/sdk/docs/tutorials/project_wizard/adding_new_steps.html)しかなく、カスタムフィールドの作成方法や、ファイルの追加方法、ファイルの編集方法などがありませんでした。

そこで、このエントリーでその辺りの説明をしたいと思います。

# トピック

* カスタムビューの追加方法
* `ModuleWizardStep`内での入力情報の取得方法
* ファイル作成方法
* ファイルの内容変更方法

# カスタムビューの追加方法

カスタムビューの追加方法は正直言うとかなり簡単です。

```java
@Override
public JComponent getComponent() {
    return new JLabel("Provide some setting here");
}
```

このメソッド内でカスタムビューを作成し、returnすればOKです。

もし、カスタマイズした`ModuleWizardStep`をformファイルにバインドしているのであれば、ルートのpanelをreturnするだけです。

自分の場合、[フォームファイル](https://github.com/shiraji/intellij-plugin-with-gradle-wizard/blob/1.0.1/src/main/kotlin/com/github/shiraji/ipgwizard/step/IPGWizardSupportLanguageStep.form)
これをこっちにバインドしています。
 [IPGWizardSupportLanguageStep](https://github.com/shiraji/intellij-plugin-with-gradle-wizard/blob/1.0.1/src/main/kotlin/com/github/shiraji/ipgwizard/step/IPGWizardSupportLanguageStep.java).

最終的に出来上がるのが

![language](https://raw.githubusercontent.com/shiraji/intellij-plugin-with-gradle-wizard/master/website/images/language.png)

"Project SDK"フィールドを作成するには、`getModuleType()`を継承する必要があります。

例えば、スタンダードなJava SDKを追加したい場合、以下のようにKoltinで記述します。

```kotlin
override fun getModuleType() = StdModuleTypes.JAVA
```

# `ModuleWizardStep`内での入力情報の取得方法

ユーザが"Next"ボタンをクリックすると、`ModuleWizardStep#updateDataModel()`が呼ばれます。このメソッド内で値を取得したり、保存したりします。

自分の場合、かなり設計がクソだけど、builderに直接設定しちゃってます。(動けばええやろ？)

https://github.com/shiraji/intellij-plugin-with-gradle-wizard/blob/1.0.1/src/main/kotlin/com/github/shiraji/ipgwizard/step/IPGWizardSupportLanguageStep.java#L43-L46

```java
@Override
public void updateDataModel() {
    builder.setLanguage(languages.get(languageComboBox.getSelectedIndex()));
}
```

# ファイル作成方法

ファイルを新しいプロジェクトに追加するには、`VfsUtil.saveText(VirtualFile, text)`を使います。

intellij-communityのレポジトリにめっちゃ良いメソッドがありました。

https://github.com/JetBrains/intellij-community/blob/9d6477485d37e796036ee524788231f2f47e6ada/plugins/gradle/src/org/jetbrains/plugins/gradle/service/project/wizard/GradleModuleBuilder.java#L332-L360

これ公開してくれたらいいのにね。

# ファイルの内容変更方法

IntellijのFile templateの仕組みを使います。File templateの[File and Code Templates](https://www.jetbrains.com/help/idea/2016.1/file-and-code-templates.html)を読むと良いです。

一つ、条件文に関して記載がなかったので、追加で説明します。条件文の文法は以下です。

```
#if (${LANGUAGE} && ${LANGUAGE} == "Kotlin")
apply plugin: 'kotlin'
#else
apply plugin: 'java'
#end
```

spaceいっぱい生成しちゃうのでインデント気をつけて下さい。
