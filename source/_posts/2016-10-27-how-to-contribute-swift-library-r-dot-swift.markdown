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

Swiftでprintlnを書いてハマるレベルのエンジニアが一週間でR.swiftにコントリビュートしたお話。

Swift開発している人にとっては常識な話が多数だと思いますが、Android開発してて、Swiftもやってみたい！とかSwiftのライブラリ開発してみたい！という人向けです。

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


| 名前 | ライブラリ概要 | 検討した理由 | 検討した理由 |
|:------------:|:------------:|:------------:|:------------:|
|[Himotoki](https://github.com/ikesyo/Himotoki)|タイプセーフなJSONデコードライブラリ|メンテナーが日本人のikesyoさんという安心感から。(恐れ多くて関わったことがないのですが・・・)|* issueなし！(スゲェ・・・。|
|[Kingfisher](https://github.com/onevcat/Kingfisher)|Imageのダウンロード・キャッシングライブラリ|なんか名前がかっこよかった|*  開発活発 * ラベリングがない|
|||||
|||||

## [Kingfisher](https://github.com/onevcat/Kingfisher)

### ライブラリ概要

Imageのダウンロード・キャッシングライブラリ

### 検討した理由

なんか名前がかっこよかった

### 検討結果

*  開発活発
* ラベリングがない

## [Alamofire](https://github.com/Alamofire/Alamofire)

### ライブラリ概要

Swift版HTTPネットワーク

### 検討した理由

Swiftやるならこれでしょという短絡的な発想

### 検討結果

* 開発活発
* ラベリングあり
* メインテナー多そう
* HTTP周りの知識必須っぽい

## [R.swift](https://github.com/mac-cain13/R.swift)

### ライブラリ概要

コンパイルタイムでコード生成し、Androidで言うRクラスを使えるようにするライブラリ。

### 検討した理由

自分がAndroid出身者だから。

### 検討結果

* ラベリングがしっかり
* メインテナーが複数いるっぽい
* 自分が持っている知識でいけるっぽい

ということで、[R.swift](https://github.com/mac-cain13/R.swift)を選択しました。

# 実際に勉強出来た事

ここから1週間強でprintすらできなかったエンジニアが学んだこととSwiftライブラリコントリビュートに必要な知識や参考にしたエントリーを記載します。

たぶんiOSエンジニアを数ヶ月やればそれくらい知ってるわ！という知識だと思いますが。。。

## ライブラリのビルド方法
## サンプルアプリのビルド方法
## Optional
## guard文
## class/struct
## Cライブラリの導入方法
## リフレクション？
## XCTestの書き方
##

# 感想

メインテナーがPRを最初に投げて、昇華していこうぜスタイルの自分にとって理想的な人だった。これのおかげで、わかる点までやってみて結果を貼って、質問するスタイルで勉強しつつコードを完成させることが出来ました。

みんな使ってね！
