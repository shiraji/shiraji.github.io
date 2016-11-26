---
layout: post
title: "How to create custom Intention"
date: 2016-11-26 10:26:25 +0900
comments: false
categories: Intellij plugin Kotlin 
keywords: Intellij plugin kotlin Kotlin Intention custom
description: Describe how to create Intellij custom Intention
---

Intellij IDEAだけではないのですが、Intentionは自分でカスタムしたものを作ることが可能です。

Intentionが何か？というのは以下の記事に説明があります。

IntelliJ IDEAクイックスタート – インテンション
http://samuraism.com/products/jetbrains/intellij-idea/quickstart/intentions

元々Android Studio出身のためでもあるのですが、以下の記事でIntentionの利用シーンが書いてあります。
http://qiita.com/yuya_presto/items/813c19513c2771815b28

# 自己紹介

自分のページにリストされているIntellijプラグインページがページングされたらプラグイン開発辞めようと誓っていて、最近中の人と話したらページング機能ないけど？ｗと生涯現役プラグイン開発者としてやっていくことが決定した人間です。

Intentionだけではなく、Inspectionなども作成しており、Kotlinのコントリビュートもしています。Kotlinの主なコントリビュートもInspection/Intentionなので、説明くらいは出来るレベルだと思います。

# 想定読者

* Intentionを作ってみたい・プラグイン開発に興味がある
* Intellijの機能をある程度理解している

