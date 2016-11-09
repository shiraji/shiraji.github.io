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

ここから1週間でprintすらできなかったエンジニアが学んだこととSwiftライブラリコントリビュートに必要な知識や参考にしたエントリーを記載します。

## ライブラリのビルド方法

Xcodeを最新化し、ソースの準備まで終わりました。で？これからどうすればいいの？という状況になりました。残念ながらレポジトリのREADMEにもビルド方法は書かれていません。たぶんiOS開発者には当たり前なのだと思います。

Android開発しかしていない人にとって辛いのはiOSアプリやライブラリをコマンドラインでビルドするのは大変だということです。一瞬Eclipse時代を思い出しそうになりますが

Xcodeを立ち上げ、`プロジェクト名.xcworkspace`を開きます。R.swiftでは`R.swift.xcodeproj`というファイルを開きたくなりますが、`R.swift.xcworkspace`を開きます。R




## サンプルアプリのビルド方法
## Optional
## guard文
## class/struct
## Cライブラリの導入方法
## リフレクション？

[Mirror](https://developer.apple.com/reference/swift/mirror)を使います。

残念ながら、採用しなかったのですが、テストを書くときに特定のプロパティが存在しているかのチェックを行いたく、色々調べました。

ただ、どうしてもうまく使えず断念しました。

```swift
let color = R.color.this
let mirror = Mirror(reflecting: color)
```

この段階でmirrorがほぼほぼ何も持っていない空インスタンスになってしまっていました。

以下なら動く。

```swift
let setting = R.string.settings
let mirror = Mirror(reflecting: color)
```

たぶん`struct`とか`this`とかが影響しているのだろうと推測しましたが、何分デバッグ方法すら検討つかず、断念しました。

## XCTestの書き方

上記のリフレクションの時に`XCTest`なるものを触りました。



# 感想

メインテナーがPRを最初に投げて、昇華していこうぜスタイルの自分にとって理想的な人だった。これのおかげで、わかる点までやってみて結果を貼って、質問するスタイルで勉強しつつコードを完成させることが出来ました。

みんな使ってね！
