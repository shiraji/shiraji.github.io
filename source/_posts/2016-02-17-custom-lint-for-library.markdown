---
layout: post
title: "Custom lint for library"
date: 2016-02-17 3:00:00 +0900
comments: false
categories: Android, lint
---

# Summary

* PermissionsDispatcher now have a custom lint
* Adding custom lint is not that hard
* LintDetectorTest...? Forget it

# PermissionsDispatcher now have a custom lint

First of all, I am one of collaborators of [PermissionsDispatcher](https://github.com/hotchemi/PermissionsDispatcher) which makes developers handle runtime permissions very easily.

PermissionsDispatcher now have a custom lint for its library. Initial issue came from [this comment](https://github.com/hotchemi/PermissionsDispatcher/issues/56#issuecomment-163499243).

The library requires the developer to define a method with `@NeedsPermission`. Like this

```java
@NeedsPermission(Manifest.permission.CAMERA)
void showCamera() {
}
```

However, this method should not directly access. Instead, the developers must use a method that PermissionsDispatcher generated.

```Java
void foo() {
    MainActivityPermissionsDispatcher.showCameraWithCheck(this); // Yes
}

void foo2() {
    showCamera(); // No!
}
```

In order to make sure the developers did not call this method, we decide to provide custom lint for this library.

# How to add custom lint for library

To create custom lint for Android application, check links below

* [Writing Custom Lint Rules](http://tools.android.com/tips/lint-custom-rules)
* [Writing a Lint Check](http://tools.android.com/tips/lint/writing-a-lint-check)
* [@hotchime's blog post at qiita](http://qiita.com/hotchemi/items/9364d54a0e024a5e6275) (For Japanese developer)

Custom lint for library needs extra settings to inject `lint.jar` to its .aar file

```groovy
configurations {
    lintChecks
}

dependencies {
    ...

    // we have lint module with lintCheck configuration.
    // check this file: https://github.com/shiraji/PermissionsDispatcher/blob/master/lint/build.gradle
    lintChecks project(path: ':lint', configuration: 'lintChecks')
}

assemble.doLast {
    copyJarToOtherModules.execute()
}

task copyLintJar(type: Copy) {
    from(configurations.lintChecks) {
        rename { 'lint.jar' }
    }
    into 'build/intermediates/lint/'
}

project.afterEvaluate {
    def compileLintTask = project.tasks.find { it.name == 'compileLint' }
    compileLintTask.dependsOn(copyLintJar)
}
```

Basically, if there is `compileLint` task, then make the task depends on `copyLintJar` task which copies `lint.jar` to `build/intermediates/lint/`

The user of this library doesn't need to put lint.jar to `.android/lint/`. Just sync library and then run `./gradlew lint`

# Use LintDetectorTest

Just don't.

Check this code

```Java
File f = new File(root,
            "tools/base/lint/libs/lint-tests/src/test/java/".replace('/', File.separatorChar)
            + pkg.replace('.', File.separatorChar)
            + File.separatorChar + path);
```

This is the code that reads from `$ROOT/tools/base/lint/libs/lint-tests/src/test/java/PACAKGE-NAME-REPLACE-DOT-WITH-SLASH`.
I believe most developers doesn't want to put project in the specific directory in order to pass the test cases.

Actually, current version of `LintDetectorTest` has a lots of problems with loading resources, java source and "class" file. (Yes! you need to supply .class file to run the test)

In order to use the `LintDetectorTest`, the developer must override `protected InputStream getTestResource(String relativePath, boolean expectExists)` like [what AOSP did](https://android.googlesource.com/platform/tools/base/+/master/lint/libs/lint-tests/src/test/java/com/android/tools/lint/checks/AbstractCheckTest.java?autodive=0%2F%2F%2F%2F%2F%2F%2F#49)

`LintDetectorTest` is still Beta. I assumed there are a lot of work to make it production release.

If it comes to production, I hope it does not require me to pass .java/.class file like this way.

```Java
lintFiles("MainActivity.class=>pkg/MainActivity.class"));
```

# Now how to test???

I don't know.

The only way I came up is [manual testing](https://github.com/hotchemi/PermissionsDispatcher/pull/75#issuecomment-178650850).

So, someone who know better way to test lint, please let me know!
