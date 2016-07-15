---
layout: post
title: "How to Kontribute (for Japanese)"
date: 2016-07-14 13:07:08 +0900
comments: false
categories: kotlin, github, OSS
keywords: kotlin, contribute, OSS, kontribute, GitHub
description: Kotlinへのcontribute時に躓いたところを書きます。
---

This entry is for Japanese. For English speaker, please refer [kotlin repo](https://github.com/JetBrains/kotlin)

# 最初に

kotlinにcontributeし始めました。ドキュメントに書いてない部分があったので、主に自分用になりますが、書いておきます。

この文章は2016/07に書かれたので、その後、contribute方法が変わっているかもしれません。

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


contributeする前に[README](https://github.com/JetBrains/kotlin/blob/master/ReadMe.md)は必ず読み込んでください。

# 開発環境

## JDK周り

特に、JDKの設定は複雑です。jdk1.6, 1.7, 1.8全てが必要で、それらをドキュメント通り指定します。

```
JDK_17=`/usr/libexec/java_home -v 1.7`
```

こんな感じで設定していきました。

## build

pullには結構時間かかります。buildコマンドはドキュメントにある通り、

```
ant -f update_dependencies.xml
ant -f build.xml
```

kotlin開発用プラグインのインストールも必要です。

全てが揃わない限り、ビルド出来ません。このあたりで断念するのはもったいないので、わからなければ、slackのkotlinlangのkontributorチャンネルで聞いてください。

kotlinlangのslackはhttps://kotlinlang.org/community.htmlここに詳細があります。

## run

.idea/runConfigurationを見てもらうとわかるのですが、かなり多くのrun設定が含まれています。

ビルドが成功すれば、それらをrunするだけで、動きます。IDEAをrunすると自分の修正を取り込んだIntellijが立ち上がります。

### run test

UnitテストもIDEAなどと同じでrun設定から起動します。

一つ一つのテストを起動することも出来ますが、working directoryがプロジェクトrootを指定しない限り動かないので注意。All IDEA Plugin Testsの設定を参考にしてください。

##

なぜか動かないマシンがあった。ideaSDK/を消したら動くらしいが、それでも動かず。

# 開発

READMEにもありますが、kotlinにはまだまだいっぱいやることがあるので、contributeし放題です。

[YouTrack](https://youtrack.jetbrains.com/oauth?state=%2Fissues%2FKT%3Fq%3Dtag%3A%2520%257BUp%2520For%2520Grabs%257D%2520%2523Unresolved)からやりたいものを選んで下さい。

そのスレッドにやるわこれ！とコメントすると良いです。やっていいのかわからない場合は、kontributorチャンネルでやっていいか？という質問を投げて下さい。JetBrainsの営業時間になればだいたい回答が来ます。

## ブランチ名

特にルールないらしい。なんでも良い。masterはさすがにマナー違反だと思う。

## コミットメッセージ

あんまりルールがあるように見えない。`#KT-12345 Fixed`はつけたほうが良さ気。

## Pull Request

特に気にせず投げてOK。

投げた後に、そのURLを対象のYouTrackのissueのコメントに貼り付けて欲しいとのこと。

## マージ

マージはcherry-pick？でマージ先をJetBrainsの中の人がやってくれる。微修正までしてくれる。Authorは自分になる。(CommiterがJBの中の人。)

こんな感じ。

https://github.com/JetBrains/kotlin/commits?author=shiraji

<script async src="//pagead2.googlesyndication.com/pagead/js/adsbygoogle.js"></script>
<!-- 728x90 -->
<ins class="adsbygoogle"
     style="display:inline-block;width:728px;height:90px"
     data-ad-client="ca-pub-3940616565912592"
     data-ad-slot="7693358062"></ins>
<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

# 雑感

殺伐としておらず、やりたい！と手をあげたらやらせてくれる。ちょっとわかんね。となっても質問すれば教えてくれる。

kontributorチャンネルで聞けばだいたい問題は解決する。やってほしいIssueとかも聞けば答えてくれる。非常にやりやすい感じでした。

IDE周りは今まで結構コード書いてたり読んでたりしているので、引き続きcontributeしていこうかなと考えています。
