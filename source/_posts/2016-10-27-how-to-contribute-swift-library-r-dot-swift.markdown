---
layout: post
title: "how to contribute R.swift (for Japanese Android Engineer)"
date: 2016-10-27 16:55:32 +0900
comments: false
categories: iOS Swift OSS iPhone
keywords: iOS Swift OSS iPhone
description: Describe what I lean about contributing Swift library
---

This entry is for Japanese speaker. Those who are interested to contribute R.swift, please refer to [this doc](https://github.com/mac-cain13/R.swift/blob/master/Documentation/Contribute.md)

Swiftでprintlnを書いてハマるレベルのエンジニアが一週間(実稼働時間 約8時間)でR.swiftにコントリビュートしたお話。

Swift開発している人にとっては常識な話が多数だと思いますが、Android開発してて、Swiftもやってみたい！とかSwiftのライブラリ開発してみたい！という人向けです。

# 想定読者

* Android開発はしていて、iOSの開発も興味がある
* Swiftのライブラリにコントリビュートしてみたいけど、したことがない

申し訳ないですが、iOS/Swift開発経験が長い人は想定外です。

# 自己紹介

Kotlinが好きなAndroidエンジニアです。

* Swift力: printlnとか書いて、コンパイルエラーになって、なんでや？って悩むくらい
* Xcode力: 新規ファイル作成は任せろ！
* その他: cocoapodは知ってた。ObjCは書いたことあったけど、ブランクが2年ほどある。

iOS開発は素人に毛が生えた程度のレベルの人間ということです。

# 経緯

* 会社でiOSチームがSwift3を検証していた。
* 自分もやりたいけど業務中に勉強する時間がない。
* あ、そういえば、こんな[記事](http://shiraji.github.io/blog/2016/01/10/pull_req_study/)が・・・
* やるか！

# コントリビュートできそうなプロジェクト選定

プルリク駆動開発のルールに則って、勉強できそうな星多いライブラリを探してみた。

| 名前 | ライブラリ概要 | 検討した理由 | 検討結果 |
|:------------:|:------------:|:------------:|:------------:|
|[Himotoki](https://github.com/ikesyo/Himotoki)|タイプセーフなJSONデコードライブラリ|メンテナーが日本人のikesyoさんという安心感から。<br>(恐れ多くて関わったことがないのですが・・・)|issueなし！(スゲェ・・・。|
|[Kingfisher](https://github.com/onevcat/Kingfisher)|Imageのダウンロード・キャッシングライブラリ|なんか名前がかっこよかった|開発活発<br>ラベリングがない|
|[Alamofire](https://github.com/Alamofire/Alamofire)|Swift版HTTPネットワーク|Swiftやるならこれでしょという短絡的な発想|開発活発<br>ラベリングあり<br>メインテナー多そう<br>HTTP周りの知識必須っぽい|
|[R.swift](https://github.com/mac-cain13/R.swift)|コンパイルタイムでコード生成<br>Androidで言うRクラスを使えるようにするライブラリ|自分がAndroid出身者だから|ラベリングがしっかり<br>メインテナーが複数いるっぽい<br>自分が持っている知識でいけるっぽい|

ということで今回は[R.swift](https://github.com/mac-cain13/R.swift)を選択しました。

# 実際に勉強出来た事

今回のコントリビュートで以下の項目を勉強しました。

* ライブラリのビルド方法
* サンプルアプリのビルド方法
* Swiftの文法(optional/guard/コレクション処理など)
* Cライブラリ/Cコードの導入方法
* リフレクション?(Mirror)
* XCTestの書き方

なかなか濃い一週間でした。

## ライブラリのビルド方法

ここをrswiftに変える。

*スクショ*

Runではなく、BuildでOK

## サンプルアプリのビルド方法

ここをResourceAppに変える。

*スクショ*

ライブラリのコード内に`print()`を書けば`rswift.log`というところにログが出力されるように設定されています。これを利用して、変更を確認していきます。

## Swiftの文法

自分は本を読んで勉強するというタイプではなく、try-errorで実際に触って勉強するタイプです。

Swiftの文法も書きながら覚えていきましたが、ちょくちょくこれどうやればいいの？っていうのにぶつかりました。特にKotlinのsmartcastの知識が邪魔してきました。。。`if let guard`などは文法が結構特殊なので最後まで慣れませんでした。あと、一番の問題はSwift3を調べたいのですが、大量にSwift2などの古い日本語ドキュメントが出てきます。iOS開発者辛いなーという感じでした。OptionalもコンセプトはKotlinと似ているのですが・・・。この辺りでKotlinとSwiftが似ていると言っていた人を罵りたくなってました。

コレクション処理に関してはRubyやJava8、Kotlinなど他の言語でもよくあるものなので、そこまで苦しまなかったです。

## Cライブラリ/Cコードの導入方法

Androidであれば、NDKを入れて、Cコードとjnlを用意してーと本当に多くのことをやらないとCコードを利用することは出来ません。

ここはObjCの強みで、

Cライブラリはここここ

Cコードだけここここ

こんなことで利用出来てしまうんか！と驚愕しました。

## リフレクション?(Mirror)

SwiftにもJavaのリフレクションがありました。`Mirror`というクラスを利用します。実はこれ使い方が間違っていたのかうまく動きませんでした。

```swift
```

ちなみにこのコードであれば動きます。

```swift
```

たぶんthisとかのせいだろうなーと思いましたが、深追いはせず。

# R.swiftの開発方法



ある程度開発した後に気づいたので、今回は利用しなかったのですが、XCTestもあり、TDD開発も可能です。

メインテナさんがPRを最初に投げて、昇華していこうぜスタイルの自分にとって理想的な人でした。これのおかげで、わかる点までやってみて結果を貼って、質問するスタイルで勉強しつつコードを完成させることが出来ました。

みんな使ってね！