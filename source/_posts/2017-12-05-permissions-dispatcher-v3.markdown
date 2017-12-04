---
layout: post
title: "PermissionsDispatcher v3に関して"
date: 2017-12-05 00:01:38 +0900
comments: false
categories: Android PermissionsDispatcher Kotlin
keywords: Android PermissionsDispatcher Kotlin KotlinPoet
description: Describe what's new about PermissionsDispatcher v3
---

この記事は [Android Advent Calendar 2017](https://qiita.com/advent-calendar/2017/android) の5日目の記事です。

## はじめに

[PermissionsDispatcher](https://github.com/permissions-dispatcher/PermissionsDispatcher) v3が今年の9月にリリースされました🎉

<blockquote class="twitter-tweet" data-lang="ja"><p lang="en" dir="ltr">Finally PermissionsDispatcher 3.0.0 is out😄 We added fully <a href="https://twitter.com/kotlin?ref_src=twsrc%5Etfw">@kotlin</a> support and a few improvements🎉Enjoy✌️ <a href="https://t.co/3a1SQ17AWb">https://t.co/3a1SQ17AWb</a></p>&mdash; hotchemi (@hotchemi) <a href="https://twitter.com/hotchemi/status/908889575208116229?ref_src=twsrc%5Etfw">2017年9月16日</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

結構大きな修正であったので、これに関して書きます。

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

## 自己紹介

PermissionsDispatcherのコミッターの一人です。今年はそれほどコードでの貢献が出来なかったので、この記事を書くことで何か貢献したいと思います。

## 想定読者

* PermissionsDispatcherを使ったことがある人
* PermissionsDispatcher v3に興味がある人

## 書いていないこと

* PermissionsDispatcherの使い方
* KotlinPoetに関して

## PermissionsDispatcher v3への移行方法

まずv2を使っている人はv3への移行をする必要があります。移行手順は以下にあります。

https://github.com/permissions-dispatcher/PermissionsDispatcher/blob/master/doc/migration_guide.md#migrating-to-3x

簡単に解説すると以下の二点の修正が必要になります。

* Kotlinユーザは`XxxPermissionsDispatcher`クラスが無くなり、`Activity`/`Fragment`の拡張関数として提供されるようになった
* Java/Kotlin共に`XxxWithCheck`メソッドが`XxxWithPermissionCheck`メソッドに変更になった

## 生成されるKotlinコード

実際に生成されるKotlinのソースを見てみましょう。[sample-kotlinモジュール](https://github.com/permissions-dispatcher/PermissionsDispatcher/tree/123e50a184e058ad6b4adb0b659a343d79813913/sample-kotlin)では以下のコードが生成されます。

```kotlin
private val REQUEST_SHOWCAMERA: Int = 0

private val PERMISSION_SHOWCAMERA: Array<String> = arrayOf("android.permission.CAMERA")

private val REQUEST_SHOWCONTACTS: Int = 1

private val PERMISSION_SHOWCONTACTS: Array<String> = arrayOf("android.permission.READ_CONTACTS", "android.permission.WRITE_CONTACTS")

fun MainActivity.showCameraWithPermissionCheck() {
  if (PermissionUtils.hasSelfPermissions(this, *PERMISSION_SHOWCAMERA)) {
    showCamera()
  } else {
    if (PermissionUtils.shouldShowRequestPermissionRationale(this, *PERMISSION_SHOWCAMERA)) {
      showRationaleForCamera(MainActivityShowCameraPermissionRequest(this))
    } else {
      ActivityCompat.requestPermissions(this, PERMISSION_SHOWCAMERA, REQUEST_SHOWCAMERA)
    }
  }
}

fun MainActivity.showContactsWithPermissionCheck() {
  if (PermissionUtils.hasSelfPermissions(this, *PERMISSION_SHOWCONTACTS)) {
    showContacts()
  } else {
    if (PermissionUtils.shouldShowRequestPermissionRationale(this, *PERMISSION_SHOWCONTACTS)) {
      showRationaleForContacts(MainActivityShowContactsPermissionRequest(this))
    } else {
      ActivityCompat.requestPermissions(this, PERMISSION_SHOWCONTACTS, REQUEST_SHOWCONTACTS)
    }
  }
}

fun MainActivity.onRequestPermissionsResult(requestCode: Int, grantResults: IntArray) {
  when (requestCode) {
    REQUEST_SHOWCAMERA ->
     {
      if (PermissionUtils.verifyPermissions(*grantResults)) {
        showCamera()
      } else {
        if (!PermissionUtils.shouldShowRequestPermissionRationale(this, *PERMISSION_SHOWCAMERA)) {
          onCameraNeverAskAgain()
        } else {
          onCameraDenied()
        }
      }
    }
    REQUEST_SHOWCONTACTS ->
     {
      if (PermissionUtils.verifyPermissions(*grantResults)) {
        showContacts()
      } else {
        if (!PermissionUtils.shouldShowRequestPermissionRationale(this, *PERMISSION_SHOWCONTACTS)) {
          onContactsNeverAskAgain()
        } else {
          onContactsDenied()
        }
      }
    }
  }
}

private class MainActivityShowCameraPermissionRequest(target: MainActivity) : PermissionRequest {
  private val weakTarget: WeakReference<MainActivity> = WeakReference(target)

  override fun proceed() {
    val target = weakTarget.get() ?: return
    ActivityCompat.requestPermissions(target, PERMISSION_SHOWCAMERA, REQUEST_SHOWCAMERA)
  }

  override fun cancel() {
    val target = weakTarget.get() ?: return
    target.onCameraDenied()
  }
}

private class MainActivityShowContactsPermissionRequest(target: MainActivity) : PermissionRequest {
  private val weakTarget: WeakReference<MainActivity> = WeakReference(target)

  override fun proceed() {
    val target = weakTarget.get() ?: return
    ActivityCompat.requestPermissions(target, PERMISSION_SHOWCONTACTS, REQUEST_SHOWCONTACTS)
  }

  override fun cancel() {
    val target = weakTarget.get() ?: return
    target.onContactsDenied()
  }
}
```

`REQUEST_*`や`PERMISSION_*`はv2では`MainActivityPermissionsDispatcher`クラス内で定義されていましたが、privateのトップレベルでの定義に変わりました。
また、`showCameraWithPermissionCheck`や`onRequestPermissionsResult`は`MainActivity`の拡張関数になっています。その為、`MainActivityPermissionsDispatcher`クラスがなくなりました。(Javaの方では存在しています。)

## なんでこんな変更になったのか？

### Kotlinの拡張関数

[KotlinPoet](https://github.com/square/kotlinpoet)の出現から、これを使ってKotlinコードの生成してみよう->どうせならKotlinっぽく拡張関数にしてみよう！という流れで拡張関数を使うことになりました。

詳細は以下のIssueを確認してみて下さい。

https://github.com/permissions-dispatcher/PermissionsDispatcher/issues/320

### XxxWithPermissionCheckへのリネーム

以下のIssueで問題提起されました。

https://github.com/permissions-dispatcher/PermissionsDispatcher/issues/355

このIssueでは漠然とした質問でしたが、Kotlinユーザに対して、拡張関数として提供する場合、`WithCheck`では意味がわからないのではないか？という話から一気にv3.0に導入する流れになりました。

## 懸念事項

### Kotlin向けのlint

PermissionsDispatcherはカスタムlintを提供していますが、Kotlin実装向けのlintの対応がまだ終わっていません。(実際の問題はUASTのKotlin対応)

この件に関するIssueは以下

https://github.com/permissions-dispatcher/PermissionsDispatcher/issues/373

### Intellij Plugin

[permissions-dispatcher-plugin](https://github.com/permissions-dispatcher/permissions-dispatcher-plugin)と言うIntellijプラグインですが、メイン開発者の [@shiraji](https://github.com/shiraji) がサボっている為、v3対応がされていません。また[もうv2の対応しなくて良くね？](https://github.com/permissions-dispatcher/permissions-dispatcher-plugin/issues/79#issuecomment-346983436)と言っており、誰からも反論がない為、急にv3のみのサポートになる予定です。

そんなにいないと思いますが・・・このプラグインを使っていて、v2のみしか使っていない人はこれを機にv3へのアップグレードをよろしくお願いいたします。多分今年度中にはプラグインの方も対応します。

## あんまり利用者関係ない話

### permissions-dispatcher orgが出来た

[@hotchemi](https://github.com/hotchemi)さんからの提案で、permissions-dispatcher orgが出来ました。おかげ様で、ますますメンテナとして頑張りたい！と思えるように。以下がそのIssue

https://github.com/permissions-dispatcher/PermissionsDispatcher/issues/353

数日前にあった[PermissionsDispatcherのプロダクトマネジメント](http://hotchemi.hateblo.jp/entry/2017/12/03/003912)にも同じようなことが記述されていました。

### 挙動を確認するテストを増やした

7月に書いた[Testing Against Annotation Processing](http://shiraji.github.io/blog/2017/07/14/testing-against-annotation-processing/)に詳細書いてあるので、読んでください!!!

## KotlinPoetに関して

PermissionsDispatcherの[この辺り](https://github.com/permissions-dispatcher/PermissionsDispatcher/tree/123e50a184e058ad6b4adb0b659a343d79813913/processor/src/main/kotlin/permissions/dispatcher/processor/impl/kotlin)を眺めるとKotlinPoetの実装の参考になると思います。[Kotlin Advent Calendar](https://qiita.com/advent-calendar/2017/kotlin) の16日目にKotlinPoetに関する投稿がされるので、そちらも参照して下さい。

## 最後に

PermissionsDispatcherは非常に良いライブラリで、新しい仕組みも積極的に取り入れています。一緒に開発しませんか？

また、最近、ビルド出来ない。使い方がわからない。などなどREADME読んでません的なissueに上がってきます。これの対応に時間を割いてしまい、開発が滞りガチです。もし、PermissionsDispatcherに対して何か貢献したい！と言う方は、 このようなissueの対応や調査などなどお手伝いして頂けると非常に助かります。

