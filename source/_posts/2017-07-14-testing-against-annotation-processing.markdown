---
layout: post
title: "Testing against annotation processing"
date: 2017-07-14 00:11:22 +0900
comments: false
categories: Kotlin, Annotation Processing, apt, Unit Test
keywords: Kotlin, Annotation Processing, apt, Unit Test, Testing, OSS, PermissionsDispatcher
description: Describe what type of testing requires for Annotation Processing
---

以下のPRを投げて、マージされました。

Add integration test cases for checking the behavior of generated code
https://github.com/hotchemi/PermissionsDispatcher/pull/339

このPRについて語ってみます。

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

## 想定読者

* Annotation Processingに興味がある人
* Annotation Processingを利用したライブラリを作っている人
* PermissionsDispatcherに興味がある人
* 細かい単語を気にしない人

## compile-testingによるテスト

Annotation Processingのテストで検索するとだいたい[compile-testing](https://github.com/google/compile-testing)を使えと書かれています。

compile-testingとは

> A library for testing javac compilation with or without annotation processors

です。annotation processingのライブラリでよく使われる手法として、コードを読み込ませ、compile-testingを利用し想定通りのコードを出力しているかを確認しています。

こんな感じで書いています。

https://github.com/shiraji/kenkenpa/blob/master/kenkenpa-compiler/src/test/java/com/github/shiraji/kenkenpa/compiler/HopTests.java#L17-L75

* ユーザが書くであろうコード(JavaFileObject source)からcompile-testingが生成するコードが想定している生成コード(JavaFileObject expectedSource)と等しいかどうかを確認します。
* コンパイルエラーなどが起こさないか？の確認もします。

このテストを実行し、JUnitが全てパスした場合、思った通りのコードが生成され、コンパイルも問題なしです。私はこれだけで安心していました。

## 挙動のテスト

compile-testingが通りました！OKです！・・・でも本当にそうなんでしょうか？

テストしたのはcompilerが思った通りのコードを生成したことを確認しただけです。

本当に必要なテストは、生成したコードが実際に思った通り動くかどうかの挙動の確認であるはずです。

ライブラリ利用者からすれば、極論、生成されるコードなんてどんな形でも良いのです。挙動さえ正しければ良いはずです。(メソッド数や容量とか気になるけど。）
が、その挙動の確認はcompile-testingでは行っていません。

## ではどうするか？

実際に生成したコードに対してテストを書けば良いはずです。

冒頭のPermissionsDispatcherの[PR](https://github.com/hotchemi/PermissionsDispatcher/pull/339)でやったこととして

* testモジュールを作成した(設定による生成コードの違いがあるのでtest-v13も作っています。)
* testモジュール内で実際にPermissionsDispatcherを利用するコードを書いた https://github.com/hotchemi/PermissionsDispatcher/pull/339/files#diff-fd72dbeb86d7072fd2fc764574b85633
* 上記のコードで生成されるコードに関するテストを書いた https://github.com/hotchemi/PermissionsDispatcher/pull/339/files#diff-178f9383e58a228e87ded8c49a97b492

PermissionsDispatcherは[Delegate to generated class](https://github.com/hotchemi/PermissionsDispatcher#2-delegate-to-generated-class)にある通り、2つのpublicメソッドを生成します。そこで、この2つのメソッドに対し、PowerMockito、Robolectricなどフル活用してテストを書きました。

このテストのメリットとして、今後PermissionsDispatcherは[KotlinはKotlinらしいコードを生成しようぜ！](https://github.com/hotchemi/PermissionsDispatcher/issues/320)という話が上がっており、Kotlinらしいコードを生成したとしても、このテストが通りさえすれば、あくまで理論上ですが、どんなコードを生成しても問題ないと言えるはずです。

## compile-testing捨てるべきなの？

捨てちゃダメです。冒頭のPRでは生成コードが必須になるため、正常系のテストしか行うことが出来ません。コンパイルエラーが発生した場合のエラーハンドリングが適切に行われているか？の確認としては非常に有効に活用できます。

compile-testingが何をテストしているのかをしっかり把握し、適材適所でのライブラリの選定が一番だと思います。