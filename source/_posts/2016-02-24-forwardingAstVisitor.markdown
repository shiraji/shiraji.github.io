---
layout: post
title: "ForwardingAstVisitor's Life Cycle (Android Lint)"
date: 2016-02-24 14:37:58 +0900
comments: false
categories: Android, lint
---

# Summary

* `ForwardingAstVisitor` has life cycle
* visitXxx -> afterXxx -> endXxx
* Begin `visitCompilationUnit`, End `afterVisitCompilationUnit`
* Go from top to bottom. No surprise, except annotation.
* Does not talk about visitIf or any of visitXxx where Xxx is keyword

<script async src="//pagead2.googlesyndication.com/pagead/js/adsbygoogle.js"></script>
<!-- 728x90 -->
<ins class="adsbygoogle"
     style="display:inline-block;width:728px;height:90px"
     data-ad-client="ca-pub-3940616565912592"
     data-ad-slot="7693358062"></ins>
<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

# Motivation

The past few weeks, I am working on creating custom lints for [PermissionsDispatcher](https://github.com/hotchemi/PermissionsDispatcher).

Using `ForwardingAstVisitor` is the easiest way to implement custom lint. I found there are 75 "visit" methods in the class. For instance, `visitMethodDeclaration` is called for iterating all method declarations. `visitIdentifier` is for all identifiers. Trust me, there is even `visitIf` and `visitSwitch` that are called for each `if` and `switch`.

However, I could not find any documentations that describes which "visit" method comes first. So...this is the post that describes what is the "lifecycle" of `ForwardingAstVisitor`.

# Basic Rules

There are 4 types of methods, `visit`, `visitNode`, `afterVisit`, `endVisit` for `ForwardingAstVisitor`.

* `visitXxx` comes first
* `visitNode` comes second for dive into the node
* `afterVisitXxx` comes if there is no more method call inside `visitXxx`
* Finally, `endVisit` comes for wrap up.

For instance, followings are the method call for checking `@Override`:

```
visitAnnotation                     : target node -> @Override
| visitNode                         : target node -> @Override
| | visitTypeReference              : target node -> Override
| | | visitNode                     : target node -> Override
| | | | visitTypeReferencePart      : target node -> Override
| | | | | visitNode                 : target node -> Override
| | | | | | visitIdentifier         : target node -> Override
| | | | | | | visitNode             : target node -> Override
| | | | | | afterVisitIdentifier    : target node -> Override
| | | | | endVisit                  : target node -> Override
| | | | afterVisitTypeReferencePart : target node -> Override
| | | endVisit                      : target node -> Override
| | afterVisitTypeReference         : target node -> Override
| endVisit                          : target node -> Override
afterVisitAnnotation                : target node -> @Override
```

# General Rules

Now, let see what's happens if I want to lint check for a Java file.

Assuming I am going to check following class

```Java
package pkg;

import android.Manifest;
import android.support.v7.app.AppCompatActivity;
import permissions.dispatcher.NeedsPermission;
import permissions.dispatcher.RuntimePermissions;

@RuntimePermissions
public class MainActivity extends AppCompatActivity {
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
    }

    @NeedsPermission(Manifest.permission.CAMERA)
    void showCamera() {
    }

    @Override
    public void onRequestPermissionsResult(int requestCode, String[] permissions, int[] grantResults) {
        super.onRequestPermissionsResult(requestCode, permissions, grantResults);
        MainActivityPermissionsDispatcher.onRequestPermissionsResult(this, requestCode, grantResults);
    }
}
```

Here is the [debuging code](https://gist.github.com/shiraji/dc4665d704d8852243c3). It's basically Sys-out method name and `node` parameter. And the output is [this](https://gist.github.com/shiraji/37993e824c36e31724d5)

OK, Too long...let me summarize idea.

* The first visit-method is `visitCompilationUnit`
* Next it checks `visitPackageDeclaration` for `package pkg;` (Meaning, it starts from the first line of the class)
* And goes one by one until it reaches the bottom of the class
* The last after-method is `afterVisitCompilationUnit`

# Exceptional Rules

Now, we know the lint checks goes the top to the bottom. However, there is an exception, Annotation.

`@RuntimePermissions` which is located the above of the class declaration in the example above.

Actually, when we run the lint checks, `visitClassDeclaration` comes [first](https://gist.github.com/shiraji/37993e824c36e31724d5#file-output-txt-L127) and then `visitAnnotation` comes [later](https://gist.github.com/shiraji/37993e824c36e31724d5#file-output-txt-L167).

It seems that the lint handles annotation as a node inside of method declaration.

For example, if we want to store class declaration object which `@RuntimePermissions`, we cannot do followings:

```Java
List<ClassDeclaration> classDeclarations;
boolean hasRuntimePermisions;

@Override
public boolean visitAnnotation(Annotation node) {
    String type = node.astAnnotationTypeReference().getTypeName();
    if ("permissions.dispatcher.RuntimePermissions".equals(type)) {
        hasRuntimePermisions = true;
    }
    return super.visitAnnotation(node);
}

@Override
public boolean visitClassDeclaration(ClassDeclaration node) {
    if(hasRuntimePermisions) {
        classDeclarations.add(node);
    }
    return super.visitClassDeclaration(node);
}
```

Instead, I would use `afterVisitClassDeclaration` and call `requestRepeat` to run other detector which uses class declaration objects.

```Java
JavaContext context;
List<ClassDeclaration> classDeclarations;
boolean hasRuntimePermisions;

@Override
public boolean visitAnnotation(Annotation node) {
    String type = node.astAnnotationTypeReference().getTypeName();
    if ("permissions.dispatcher.RuntimePermissions".equals(type)) {
        hasRuntimePermisions = true;
    }
    return super.visitAnnotation(node);
}

@Override
public void afterVisitClassDeclaration(ClassDeclaration node) {
    if(hasRuntimePermisions) {
        classDeclarations.add(node);
        context.requestRepeat(new MyOtherDetector(), EnumSet.of(Scope.ALL_JAVA_FILES));
    }
    return super.visitClassDeclaration(node);
}
```
