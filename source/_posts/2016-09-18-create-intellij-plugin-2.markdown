---
layout: post
title: "Tips for creating intellij plugin in Kotlin"
date: 2016-09-18 21:14:03 +0900
comments: false
categories: Java, Kotlin, Android, Development, Intellij Plugin
keywords: Java, Kotlin, Intellij IDEA Plugin, Android Studio, PSI, Intellij Plugin,
description: Describe how to use PSI classes when developing Intellij Plugin
---

This is the second blog post for "Tips of creating Intellij plugin".

This entry is focus on tips for creating intellij plugin in Kotlin. (But I still believe it is useful for Java developers.)

For more basic tips, please read [Tips for Creating Intellij Plugin](http://shiraji.github.io/blog/2016/03/28/create-intellij-plugin/)

# Topics

* How to find parent
* How to find children
* How to find files

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

# How to find parent

Finding parent is just calling `PsiElement#getParent()`. However, in most case, I want to find a specific type of parent. In that case, using extension methods in `psiUtils.kt` is the best way to do it. [psiUtils.kt](https://github.com/shiraji/kotlin/blob/1.0.3/compiler/frontend/src/org/jetbrains/kotlin/psi/psiUtil/psiUtils.kt#L115-L129) provides variety of useful extension methods. 

For instance, if I want to find the function that the element belong,

```kotlin
val parentFunc = element.getStrictParentOfType<KtNamedFunction>()
```

This method return `null` if there is no applicable element. (In the example, if the element is not located inside a function, it will be null.)

For Java developers, use `PsiTreeUtil.getParentOfType()`

# How to find children

After finding `psiUtils.kt`, I was expected to find children using something like `getStrictChildrenOfType` or something. Guess what. I was wrong. There are more useful methods in [psiUtils.kt](https://github.com/shiraji/kotlin/blob/1.0.3/compiler/frontend/src/org/jetbrains/kotlin/psi/psiUtil/psiUtils.kt#L173-L233). The reason why it is not `getStrictChildrenOfType` is I guess it requires to recursive tree visiting. 

To find all children of specific types, use `collectDescendantsOfType`

```kotlin
element.collectDescendantsOfType<KtExpression>()
``` 

To find existance of children of specific type, use `anyDescendantOfType` 

```kotlin
element.anyDescendantOfType<KtNamedFunction>()
```

To find the one child of specific type, use `findDescendantOfType`

```kotlin
element.findDescendantOfType<KtFunctionLiteral>()
```

# How to find files

When I want to iterate though all resource files in my project, following one liner archive it 

```kotlin
FileTypeIndex.getFiles(XmlFileType.INSTANCE, ProjectScope.getProjectScope(project))
```

(Watch out `.idea/` folder. It is also include as "Project" so this line also grabs all xml files in ".idea".)

This method find all files in specific types of specific scope. The above line finds all xml files in my project. 
