---
layout: post
title: "How to Kontribute (v3) - Recommended way"
date: 2018-03-17 00:00:03 +0900
comments: false
categories: Kotlin OSS GitHub
keywords: Kotlin Kontribute Kontributor
description: Describe how to contribute Kotlin in 2018
---

This blog post describes how to start Kontribute or how to start contributing [Kotlin](https://kotlinlang.org/).

This blog entry is third one. If you don't read first one, please go to http://shiraji.github.io/blog/2018/03/04/how-to-kontribute-v3-setup/

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

OK, now let's actually work with project. There are 2 recommended ways for 1st commit of Kotlin.

* Adding documentation or comment
* Providing sample codes

### Adding documentations or comment

Adding documentation or comment is one of best ways to start contribute any OSS including Kotlin. My first commit is also [fixing URL of README](https://github.com/JetBrains/kotlin/commit/68dd3dde59b0a339819407cfdf0c465809092e75)

JetBrains says that they want the documentation for standard library. Find a class or a function which are not documented well from https://kotlinlang.org/api/latest/jvm/stdlib/index.html You may think there are not much function that is not documented. Trust me, you can find at least 10 undocumented functions within 1 min!

### Providing sample codes

If you are not good English writer like me, then providing samples are the better way to start contributing Kotlin. https://youtrack.jetbrains.com/issue/KT-20357 is the issue for samples. This issue is especially for new Kontributors.

There are 4 steps to provide sample codes

![samples-steps](https://raw.githubusercontent.com/wiki/shiraji/images/blog/images/how-to-kontribute-v3/how-to-kontribute-v3.028.jpg)

Comment the target API to the issue

![first_comment](https://raw.githubusercontent.com/wiki/shiraji/images/blog/images/how-to-kontribute-v3/first_comment.png)

Then, write sample code.

```kotlin
@Sample
fun groupBy() {
}
```

The method name can be anything but don't add `test` as prefix/postfix because it is not test. Then add sample code inside the method.

```kotlin
@Sample
fun groupBy() {
    val words = listOf("a", "abc", "ab", "def", "abcd")
    val byLength = words.groupBy { it.length }

    assertPrints(byLength.keys, "[1, 3, 2, 4]")
    assertPrints(byLength.values, "[[a], [abc, def], [ab], [abcd]]")

    val mutableByLength: MutableMap<Int, MutableList<String>> = words.groupByTo(mutableMapOf()) { it.length }
    // same content as in byLength map, but the map is mutable
    assertTrue(mutableByLength == byLength)
}
```

There are no hard restriction for writing samples. You can add comment if it is helpful for other developers.

After that, in order to connect with sample code and target method, comment to the target method with `@FQN of the sample method`

```kotlin
/**
...
 * @sample samples.collections.Collections.Transformations.groupBy
 */
public inline fun <T, K> Iterable<T>.groupBy(keySelector: (T) -> K): Map<K, List<T>> {
```

Now, you are ready. Send Pull Request. Then, update you comment with pull request URL

![first_comment2](https://raw.githubusercontent.com/wiki/shiraji/images/blog/images/how-to-kontribute-v3/first_comment2.png)

That's it! Easy! Now, if you want to provide the sample code, please read README for sample code, too.
https://github.com/JetBrains/kotlin/blob/master/libraries/stdlib/samples/ReadMe.md

After the pull request get merged, in some point, you can see your sample code in official documentation http://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/group-by.html
