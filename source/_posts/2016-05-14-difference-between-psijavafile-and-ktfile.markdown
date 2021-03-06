---
layout: post
title: "Difference between generating Java and generating Kotlin for developing IntelliJ plugin"
date: 2016-05-14 23:33:07 +0900
comments: false
categories: IntelliJ Plugin, Java, Kotlin, Android
keywords: IntelliJ Plugin Development, Generate Java and Kotlin source
description: Describe what is the difference generating Java and Kotlin
---

# Motivation

I am a maintainer of [PermissionsDispatcher Plugin](https://github.com/shiraji/permissions-dispatcher-plugin) which generates Java and Kotlin for [PermissionsDispatcher](https://github.com/hotchemi/PermissionsDispatcher)
Since Kotlin is getting famous for Android developers, I thought IntelliJ plugins, which generate Android code, should support both Java and Kotlin. (By the way, [Kotlin 1.0.2](http://blog.jetbrains.com/kotlin/2016/05/kotlin-1-0-2-is-here/) now supports Android lint! This definitely will lead more developers use Kotlin!)

However, while I was developing this plugin, I found really hard to generate both Java and Kotlin code.

So, this blog post describes what are the differences between generating Java and generating Kotlin using IntelliJ plugin.

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

# Environment

Before start taking about the differences, the followings are the environment for this blog post.

* Kotlin for writing the plugin
* Gradle for build
* [gradle-intellij-plugin](https://github.com/JetBrains/gradle-intellij-plugin)

# `getClasses` for `PsiJavaFile` vs `KtFile`

```kotlin
e.getData(CommonDataKeys.PSI_FILE)
```

returns `PsiJavaFile` or `KtFile`. Both of them implements `PsiClassOwner` which means both of them has the method `PsiClass[] getClasses()`.

This method is useful for `PsiJavaFile`, it lets access classes of the file. The plugin can read/write contents of the classes.

For `KtFile`, I expected the same. I want to read/write code of classes. Yes, you can read `.class` file. It is not classes inside `.kt` file. So, even though, it has methods `add`, `addBefore` or `addAfter`, `KtFile#classes#add` throw an exception says the plugin won't be able to write contents to `.class` file!

If you want to get objects for generating code of Kotlin, then use `KtFile#getDeclarations`.

# `PsiFactory`

This could be because I could not find the best way to generate code...but PsiFactory of Java and Kotlin is different

```Kotlin
val psiElementFactory: PsiElementFactory = JavaPsiFacade.getElementFactory(project)
```

```Kotlin
val psiFactory: KtPsiFactory = KtPsiFactory(project)
```

# Generating annotations

`PsiMethod` has modifierList

```kotlin
method.modifierList.addAnnotation("Foo")`
```

On the other hand, `KtNamedFunction` has `addAnnotationEntry`

```kotlin
function.addAnnotationEntry(psiFactory.createAnnotationEntry("@Foo"))
```

To insert new line after annotation, you need to add new line manually.

```kotlin
val entry = function.addAnnotationEntry(psiFactory.createAnnotationEntry("@Foo"))
entry.add(psiFactory.createNewLine())
```

# Generating methods

`PsiClass` is easy to add method. Use `createMethodFromText` and `add` to `PsiClass`

```kotlin
val methodTemplate = """void foo() {
}""".trimMargin()
val method = JavaPsiFacade.getElementFactory(project).createMethodFromText(methodTemplate, psiClass)
psiClass.add(method)
```

For kotlin, it's almost the same.

```kotlin
val psiFactory = KtPsiFactory(project)
val function = psiFactory.createFunction("""void foo() {
}""".trimMargin())
ktClass.getBody()!!.addBefore(function, ktClass.getBody()!!.rBrace)
```
