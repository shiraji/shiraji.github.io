---
layout: post
title: "How to extend Alt+Enter"
date: 2016-12-17 00:00:01 +0900
comments: false
categories: Intellij plugin Kotlin Intention
keywords: Intellij plugin kotlin Kotlin Intention custom
description: Describe how to create Intellij custom Intention
---

この記事は[JetBrains Advent Calendar 2016](http://qiita.com/advent-calendar/2016/jetbrains)の12/17の記事です。

Intellij IDEAだけではないのですが、Alt+Enter(Intention)は自分で拡張したものを作ることが可能です。

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

# 自己紹介

自分のページにリストされているIntellijプラグインページがページングされたらプラグイン開発辞めようと誓っていて、最近中の人と話したらページング機能ないけど？ｗと生涯現役プラグイン開発者としてやっていくことが決定した人間です。

Intentionだけではなく、Inspectionなども作成しており、Kotlinのコントリビュートもしています。Kotlinの主なコントリビュートもInspection/Intentionなので、ある程度説明出来るレベルだと思います。

# 想定読者

* Intentionを作ってみたい
* プラグイン開発に興味がある
* Intellijの機能をある程度理解している
* kotlinの文法がそれなりに理解できる

# Custom Intentionを作ってみる

今回サンプルとして、Intention周りのプラグインをリリースしました。
https://plugins.jetbrains.com/plugin/9271

ソースコード
https://github.com/shiraji/databinding-support

これをベースにXMLファイルに対してのIntentionの説明をします。

今回はレイアウトファイルであり、`<layout>`タグがrootタグではなかった場合、`<layout>`タグでラップするというIntentionを作成します。

![wrap](https://raw.githubusercontent.com/shiraji/databinding-support/master/websites/images/wrap.gif)

プラグインの作成方法などは参考リンクにまとめてありますので、そちらを参照して下さい。

## Intentionクラス

`IntentionAction`を継承します。以下のメソッドを実装する必要があります。

### `getText(): String`

popupで表示される時の文字列

### `getFamilyName(): String`

よくわからねｗ(intellij-community/kotlin repo内でもgetText呼び出してるだけのところが多い。)

### `startInWriteAction(): Boolean`

Write Action内で実行するかどうか。

### `isAvailable(project: Project, editor: Editor?, file: PsiFile?): Boolean`

そのIntentionを利用可能かどうか判定する

### `invoke(project: Project, editor: Editor?, file: PsiFile?)`

Intentionが選択時に実行されるメソッド。

今回は実際に以下のようなイメージになりました。

```kotlin
package com.github.shiraji.databindinglayout.intentions

class ConvertToDatabindingLayoutIntention : IntentionAction {

    override fun getText() = "Convert to databinding layout"
    override fun getFamilyName() = "Convert to databinding layout"
    override fun startInWriteAction() = true

    override fun isAvailable(project: Project, editor: Editor?, file: PsiFile?): Boolean {
        // rootタグがlayoutではなく、androidのレイアウトファイルかどうかを判定。
    }

    override fun invoke(project: Project, editor: Editor?, file: PsiFile?) {
        // 新規<layout>タグを作成し、必要なattributeを追加し、root tagとする。
    }

}
```

https://github.com/shiraji/databinding-support/blob/1.0.2/src/main/kotlin/com/github/shiraji/databindinglayout/intentions/ConvertToDatabindingLayoutIntention.kt

このrootタグかどうかとか、androidのレイアウトファイルかどうかの判定とか、何をどう書き換えるのかとかがたぶん一番難しい部分だと思います。ただ、ここはそれぞれのIntentionやプラグインで違うのでコード読んで頑張れｗとしか言えません。。。

唯一ある共通点としてはファイルなどの読み込みや解析はPsiを使います。例えば、xmlタグだったら[XmlTag](https://github.com/JetBrains/intellij-community/blob/master/xml/xml-psi-api/src/com/intellij/psi/xml/XmlTag.java)、[XmlAttribute](https://github.com/JetBrains/intellij-community/blob/master/xml/xml-psi-api/src/com/intellij/psi/xml/XmlAttribute.java)を使います。`PsiElement#replace()`でソースコードの置換することが出来ます。

それぞれのElement作成それぞれのファイルタイプ毎にFactoryが用意されています。Xmlなら`XmlElementFactory`で作成しています。Javaの場合だと`PsiElementFactory`だし、Kotlinなら`KtPsiFactory`を使います。

## plugin.xml

Intentionクラスをplugin.xmlに定義します。

```xml
        <intentionAction>
            <className>com.github.shiraji.databindinglayout.intentions.ConvertToDatabindingLayoutIntention</className>
            <category>Android</category>
        </intentionAction>
```

https://github.com/shiraji/databinding-support/blob/1.0.2/src/main/resources/META-INF/plugin.xml#L31-L34


## Intentionの説明文を書く

自分はちょくちょく忘れるのですが・・・(実際これを忘れて、1.0.1->1.0.2のバージョン更新をした。)

こんな感じで設定画面に説明文を書くことが可能です。

![intention_settings](https://raw.githubusercontent.com/wiki/shiraji/images/blog/images/how-to-create-custom-intention/intention_setting.gif)

以下の3つのファイルを作成します。

* after.xml.template
* before.xml.template
* description.html

それぞれが表示されるのはここです。

![intention_desc](https://raw.githubusercontent.com/wiki/shiraji/images/blog/images/how-to-create-custom-intention/intention_desc.png)

例えば、JavaファイルののIntentionだった場合、`after.java.template`のようにファイル名を`after.{拡張子}.template`とすれば良いです。`before`のほうも同じです。

配置箇所は`resourcesフォルダ/intentionDescriptions/INTENSION名/`に配置します。

`ConvertToDatabindingLayoutIntention`の場合、こんな感じ。

https://github.com/shiraji/databinding-support/tree/1.0.2/src/main/resources/intentionDescriptions/ConvertToDatabindingLayoutIntention

# 最後に

Happy `alt+enter` life!

# 参考リンク

IntelliJ IDEAクイックスタート – インテンション

http://samuraism.com/products/jetbrains/intellij-idea/quickstart/intentions

プラグインプロジェクトの作成手順

http://www.jetbrains.org/intellij/sdk/docs/index.html

gradleを使う場合

http://www.jetbrains.org/intellij/sdk/docs/tutorials/build_system.html?search=gradle

このgradleプラグインのセットアップウィザードはないので、gradle-intellij-plugin wizardを使うと便利だよ(ステマ)

https://plugins.jetbrains.com/plugin/8450
