---
layout: post
title: "KotlinPoetを使ってみた"
date: 2017-12-16 00:00:01 +0900
comments: false
categories: Kotlin codegen
keywords: Kotlin kotlin KotlinPoet CodeGen codegen
description: Write about the first step of using KotlinPoet
---

KotlinPoetを使ってコード生成をしたので、触りだけですが、紹介したいと思います。

なおこのエントリーはKotlinPoet v0.6.0を利用しています。

# 想定読者

* KotlinPoetに興味がある人
* JavaPoetを触ったこと・勉強したことがある人

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

# 書いていないこと

* JavaPoetの説明

# KotlinPoetについて

KotlinPoetはKotlinのコードを生成することを手助けするライブラリです。[JavaPoet](https://github.com/square/javapoet)のKotlin版というイメージです。

以下のコードが

```kotlin
val greeterClass = ClassName("", "Greeter")
val file = FileSpec.builder("", "HelloWorld")
    .addType(TypeSpec.classBuilder("Greeter")
        .primaryConstructor(FunSpec.constructorBuilder()
            .addParameter("name", String::class)
            .build())
        .addProperty(PropertySpec.builder("name", String::class)
            .initializer("name")
            .build())
        .addFunction(FunSpec.builder("greet")
            .addStatement("println(%S)", "Hello, \$name")
            .build())
        .build())
    .addFunction(FunSpec.builder("main")
        .addParameter("args", String::class, VARARG)
        .addStatement("%T(args[0]).greet()", greeterClass)
        .build())
    .build()

file.writeTo(System.out)
```

このコードを出力します。

```kotlin
class Greeter(val name: String) {
  fun greet() {
    println("Hello, $name")
  }
}

fun main(vararg args: String) {
  Greeter(args[0]).greet()
}
```

KotlinPoetの紹介は[KotlinConfの動画](https://youtu.be/_obNBSldffw)を観ると良いです。

# KotlinPoetの考え方

実は上記のコード一点、非常に**面白い**点があります。

```kotlin
class Greeter(val name: String)
```

primary constructorの生成です。KotlinPoetの以下の部分で生成しています。

```kotlin
val file = FileSpec.builder("", "HelloWorld")
    .addType(TypeSpec.classBuilder("Greeter")
        .primaryConstructor(FunSpec.constructorBuilder()
            .addParameter("name", String::class)
            .build())
        .addProperty(PropertySpec.builder("name", String::class)
            .initializer("name")
            .build())
```

valの`name`をprimary constructorに入れるだけなのですが、KotlinPoetでは3回も`name`と記述しています。

せっかくなので、一つずつ見ていきましょう。まずPropertyを生成する場合、以下のコードになります。

```kotlin
val file = FileSpec.builder("", "HelloWorld")
    .addType(TypeSpec.classBuilder("Greeter")
        .addProperty(PropertySpec.builder("name", String::class)
            .build())
```

以下のコードが生成されます。

```kotlin
class Greeter {
    val name: String
}
```

次にprimary constructorに`name`入れたい為、primary constructorの設定を記述します。

```diff
val file = FileSpec.builder("", "HelloWorld")
    .addType(TypeSpec.classBuilder("Greeter")
        .primaryConstructor(FunSpec.constructorBuilder()
            .addParameter("name", String::class)
            .build())
+       .addProperty(PropertySpec.builder("name", String::class)
+           .build())
```

そうするとこんなコードが生成されます。

```kotlin
class Greeter(name: String) {
    val name: String
}
```

最後にprimary constructorとpropertyを連結するため、初期化方法を記述します。

```diff
val file = FileSpec.builder("", "HelloWorld")
    .addType(TypeSpec.classBuilder("Greeter")
        .primaryConstructor(FunSpec.constructorBuilder()
            .addParameter("name", String::class)
            .build())
        .addProperty(PropertySpec.builder("name", String::class)
+           .initializer("name")
            .build())
```

これでようやく以下のコードが生成されるようになります。

```kotlin
class Greeter(val name: String)
```

これは、記述されたコードは全て生成する。最適化はKotlinPoetがする。というKotlinPoetの考えからきているそうです。

KotlinConfの動画でも解説されていますので確認して見てください。 https://youtu.be/_obNBSldffw?t=20m40s

若干文法が違ったり、上記のような隠れた癖がある為、JavaPoetに慣れている方は最初戸惑うことがあるかもしれませんので、生成後のコードをしっかり確認した方が良いです。

ちなみにPermissionsDispatcherはKotlinPoetのこの挙動を知らず、[誤って外に出てしまったpropertyをコンストラクタに詰める為の修正](
https://github.com/permissions-dispatcher/PermissionsDispatcher/commit/ddf14a1dd005d5fbb9bb51b445815d6af7e7125f)をv3.0.1で入れています:joy:

その時の開発者のつぶやきです。

<blockquote class="twitter-tweet" data-lang="ja"><p lang="ja" dir="ltr">parameterとproperty作成して、property側のinitializerに変数名を入れるというまじかよ！って方法で達成した。なぜできるのか？という疑問は後で調査しよう。</p>&mdash; shiraji (@shiraj_i) <a href="https://twitter.com/shiraj_i/status/909327774044663808?ref_src=twsrc%5Etfw">2017年9月17日</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

# その他の代表的なコードの生成方法

ファイル、クラス、クラスメンバー、プライマリコンストラクター、トップレベルの関数に関しては上記サンプルコードをみてください。

それ以外のよく使いそうなコードの生成方法をメモしておきます。

## コメント

kotlinpoetのコード

```kotlin
val file = FileSpec.builder("com.github.shiraji", "HelloWorld").addComment("コメント").build()
```

生成されるコード

```kotlin
// コメント
package com.github.shiraji
```

`FileSpec.Builder`の`addComment`を利用している為、ファイル上部にコメントしていますが、Builderの種類(TypeSpec.Builderなど)によりコメント位置が調整されます。

## フォーマット

```kotlin
val message = "ふふふ"
val file = FileSpec.builder("com.github.shiraji", "HelloWorld").addComment("コメント %L", message).build()
```
```kotlin
// コメント ふふふ
package com.github.shiraji
```

フォーマットはJavaPoetと違い`%`を利用します。その他のフォーマットは[こちら](https://square.github.io/kotlinpoet/0.x/kotlinpoet/com.squareup.kotlinpoet/-code-block/index.html)を参照してください。

*これ以降のコードはあまりフォーマットを利用していませんが、本来はこのフォーマットを使う方が良いです。*

## initメソッド

```kotlin
    val file = FileSpec.builder("com.github.shiraji", "HelloWorld")
            .addType(TypeSpec.classBuilder("Greeter")
                    .addInitializerBlock(CodeBlock.builder()
                            .addStatement("val i = 10")
                            .build())
                    .build())
            .build()
```

```kotlin
package com.github.shiraji

class Greeter {
  init {
    val i = 10
  }
}
```

## Secondary Constructor

```kotlin
    val file = FileSpec.builder("com.github.shiraji", "HelloWorld")
            .addType(TypeSpec.classBuilder("Greeter")
                    .primaryConstructor(FunSpec.constructorBuilder()
                            .addParameter("name", String::class)
                            .build())
                    .addFunction(FunSpec.constructorBuilder()
                            .callThisConstructor("name") // callSuperConstructorもあります。
                            .addParameter("name", String::class)
                            .addParameter("lastname", String::class)
                            .build())
                    .build())
            .build()
```

```kotlin
package com.github.shiraji

import kotlin.String

class Greeter(name: String) {
  constructor(name: String, lastname: String) : this(name)
}
```

## 拡張関数

```kotlin
    val file = FileSpec.builder("com.github.shiraji", "HelloWorld")
            .addType(TypeSpec.classBuilder("Greeter")
                    .addFunction(FunSpec.builder("foo")
                            .receiver(String::class)
                            .build())
                    .build())
            .build()
```

```kotlin
package com.github.shiraji

import kotlin.String

class Greeter {
  fun String.foo() {
  }
}
```

## Class修飾子(Dataクラス)

```kotlin
    val file = FileSpec.builder("com.github.shiraji", "HelloWorld")
            .addType(TypeSpec.classBuilder("Foo")
                    .addModifiers(KModifier.DATA) // KModifier.ENUMなどもあります
                    .build())
```

```kotlin
package com.github.shiraji

data class Foo
```

## if/else

```kotlin
    val file = FileSpec.builder("com.github.shiraji", "HelloWorld")
            .addType(TypeSpec.classBuilder("Greeter")
                    .addFunction(FunSpec.builder("foo")
                            .beginControlFlow("if (true)")
                            .addStatement("val i1 = 10")
                            .endControlFlow()
                            .beginControlFlow("else")
                            .addStatement("val i2 = 20")
                            .endControlFlow()
                            .build())
                    .build())
            .build()
```

```kotlin
package com.github.shiraji

class Greeter {
  fun foo() {
    if (true) {
      val i1 = 10
    }
    else {
      val i2 = 20
    }
  }
}
```

## when

```kotlin
    val file = FileSpec.builder("com.github.shiraji", "HelloWorld")
            .addType(TypeSpec.classBuilder("Greeter")
                    .addFunction(FunSpec.builder("foo")
                            .addStatement("val i = 10")
                            .beginControlFlow("when(i)")
                            .beginControlFlow("10 ->")
                            .addStatement("println(\"foo\")")
                            .endControlFlow()
                            .beginControlFlow("20 ->")
                            .addStatement("println(\"foo222\")")
                            .endControlFlow()
                            .endControlFlow()
                            .build())
                    .build())
            .build()
```

```kotlin
package com.github.shiraji

class Greeter {
  fun foo() {
    val i = 10
    when(i) {
      10 -> {
        println("foo")
      }
      20 -> {
        println("foo222")
      }
    }
  }
}
```

## インデント

```kotlin
val file = FileSpec.builder("com.github.shiraji", "HelloWorld").addComment("%>コメント").build()
```

```kotlin
  // コメント
  package com.github.shiraji
```

アンインデントされるまでインデントされ続けます。

## アンインデント

```kotlin
val file = FileSpec.builder("com.github.shiraji", "HelloWorld").addComment("%>コメント%<").build()
```

```kotlin
  // コメント
package com.github.shiraji
```

# APIドキュメント

その他知りたければ、KotlinPoetの[APIドキュメント](https://square.github.io/kotlinpoet/0.x/kotlinpoet/com.squareup.kotlinpoet/index.html)を確認して下さい。(v0.x系以降のドキュメントはURLが変更されるかも？)

# 最後に

PermissionsDispatcherの[この辺り](https://github.com/permissions-dispatcher/PermissionsDispatcher/tree/123e50a184e058ad6b4adb0b659a343d79813913/processor/src/main/kotlin/permissions/dispatcher/processor/impl/kotlin)を眺めるとKotlinPoetの実装の参考になると思います。

