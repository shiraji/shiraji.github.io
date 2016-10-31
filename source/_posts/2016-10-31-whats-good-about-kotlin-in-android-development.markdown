---
layout: post
title: "What's good about Kotlin in Android development"
date: 2016-10-31 13:56:43 +0900
comments: false
categories: Android Kotlin
keywords: Android Kotlin Rx Databinding
description: describe what's good about choosing Kotlin for Android development in Japanese
---

Kotlinの文法紹介というより、Javaで書いてた辛い文法をこんな感じでKotlinで書くこと出来るよ！紹介です。

基本的に短くなりますが、長くなる書き方もあります。またこれが最強だ！と言ってるわけではなく、自分がこう書くとキモチイイ！だけです。(という予防線を張っておきます)

# 自己紹介

[Kotlin 1.0.4](https://blog.jetbrains.com/kotlin/2016/09/kotlin-1-0-4-is-here/)のexternal Contributorsの一人です。主に静的解析のところにコントリビュートしています。

Android開発もちょくちょくしており、絶賛Kotlinで移植中です。

# 前提条件と想定読者

* Android開発はある程度知っている
* KotlinでのAndroid興味がある人
* KotlinでのAndroid開発良いと言われてるけど、何がいいかわからねな人

Kotlinの文法はJavaコードと比較すればだいたいわかる感じで記載していきますが、もしわからなければ、[@shiraj_i](https://twitter.com/shiraj_i)にメンションいただければ答えますので、お気軽に質問して下さい。

# キモチイイ！文法たち

## 一行メソッド

例えば、そのある特定のテキストを返すだけのメソッドがあるとします。Javaで書くとこんな感じになります。

```java
public String getName() {
  return "MyApp";
}
```

これをKotlinだと同じようにこうかけます。

```kotlin
fun getName(): String {
  return "MyApp"
}
```

ただ、Kotlinは一行でreturn出来る場合、`=`をつけて`{}`を省略することが出来ます。

```kotlin
fun getName(): String = "MyApp"
```

さらに、戻り値が明らかな場合、指定しなくても良いので

```kotlin
fun getName() = "MyApp"
```

短いですね！

## 空メソッド

`fun foo() = Unit`

## 空クラス

Javaでは空クラスだろうと、`{}`を書かなくてはなりません。特に目印用のinterfaceとかでありますが

```java
interface Foo {}
```

Kotlinでは空クラスの場合、`{}`を書かなくて良いので

```kotlin
interface Foo
```

もちろん、classでも可能です。

```kotlin
class Bar
```

## Util系

XxxUtilsとか作って、全メソッドをstaticにして、privateコンストラクタを作って・・・みたいなやり方をちょくちょくしていました

```java
public class LogUtil {
  private LogUtil() {}

  public void initLog(String tag) {
    Timber.plant(ExtTree(tag))
  }
}
```

kotlinではTopレベルにメソッドを書けば、そういったUtil系のメソッドを書くことが出来ます。

```kotlin
fun initLog(tag: String) = Timber.plant(ExtTree(tag))
```

使い方

```kotlin
import package.initLog

fun foo() {
  initLog("MyApp")
}
```

## rx.ObservableのThread指定方法

```kotlin
val <T> Observable<T>.observeOnUI: Observable<T>
    get() = observeOn(AndroidSchedulers.mainThread())

val <T> Observable<T>.observeOnIO: Observable<T>
    get() = observeOn(Schedulers.io())

val <T> Observable<T>.observeOnComputation: Observable<T>
    get() = observeOn(Schedulers.computation())

val <T> Observable<T>.subscribeOnUI: Observable<T>
    get() = subscribeOn(AndroidSchedulers.mainThread())

val <T> Observable<T>.subscribeOnIO: Observable<T>
    get() = subscribeOn(Schedulers.io())

val <T> Observable<T>.subscribeOnComputation: Observable<T>
    get() = subscribeOn(Schedulers.computation()).unsubscribeOn(Schedulers.computation())
```

```kotlin
load()
  .subscribeOnIO
  .observeOnIO
  .subscribe()
```

```java
load()
  .subscribeOn(AndroidSchedulers.mainThread())
  .observeOn(AndroidSchedulers.mainThread())
  .subscribe();
```

## DatabindingのBindingAdapter指定方法

```kotlin
@BindingAdapter("customFont")
fun TextView.setFont(fontName: String) {
    typeface = FontCaches.fontCache.get(fontName) ?:
            Typeface.createFromAsset(context.assets, fontName).apply { FontCaches.fontCache.put(fontName, this) }
}
```

## DatabindingのonClickなどのイベント指定方法

```xml
<android.support.design.widget.FloatingActionButton
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:layout_marginTop="@dimen/spacing"
                android:onClick="@{viewModel::onClickTopFab}"
                app:backgroundTint="@color/action_green"
                app:fabSize="normal"
                app:layout_anchor="@id/app_bar"
                app:layout_anchorGravity="bottom|end"
                app:srcCompat="@drawable/plus"/>
```

```kotlin
fun View.onClickFab() {
    context.showLongToast(R.string.message_fab)
}
```

## カスタムViewのコンストラクタ

```kotlin
class SingleLineTextView @JvmOverloads constructor(context: Context, attrs: AttributeSet? = null, defStyle: Int = 0) : TextView(context, attrs, defStyle) {
    init {
        maxLines = 1
        ellipsize = TextUtils.TruncateAt.END
        setHorizontallyScrolling(true)
    }
}
```

## createIntent/newInstance

```kotlin
    companion object {
        fun createIntent(context: Context)
            = Intent(context, MyActivity::class.java).apply { }
    }
 ```

 ```kotlin
 fun newInstance(entity: DialogFragmentEntity) =
      SimpleDialogFragment().apply {
          arguments = Bundle().apply {
              putParcelable(PARCELABLE_KEY, DialogFragmentEntityParcel(entity))
          }
      }
```

nestが激しいのでこう書くともっと気持ちいい

```kotlin
fun newInstance(entity: Entity) = MyFragment().entity(entity)

private fun MyFragment.entity(entity: Entity) = apply { arguments = Bundle().entity(entity) }

private fun Bundle.entity(entity: Entity) = apply { putParcelable(KEY, entity) }
```

## setVisibleOrGone

 ```kotlin
fun View?.setVisibleOrGone(isVisible: Boolean) {
    this?.visibility = if (isVisible) View.VISIBLE else View.GONE
}
```

## BaseObservable

```kotlin
class FooObservable : BaseObservable {
    @get:Bindable
    var id: ObservableField<String>
        set(value) {
            field = value
            notifyPropertyChanged(BR.id)
        }
}
```

## Delegate

```kotlin
class MyViewModel @Inject constructor(var context: Context,
                                         var compositeSubscription: CompositeSubscription) : Subscription by compositeSubscription {
// ...
}
```

```kotlin
class ThirdActivity : AppCompatActivity() {

    @field:[Inject]
    lateinit var viewModel: MyViewModel

// ...

    override fun onDestroy() {
        super.onDestroy()
        viewModel.unsubscribe()
    }
```

## Binding(by lazy)

```kotlin
    private val binding by lazy {
        DataBindingUtil.setContentView<MyBinding>(this, R.layout.my)
    }
```

## Enum逆引き(by lazy)

```kotlin
companion object {
        private val lookup: SparseArray<VexType> by lazy {
            SparseArray<VexType>().apply {
                EnumSet.allOf(VexType::class.java).forEach {
                    this@apply.put(it.vexId, it)
                }
            }
        }

        fun fromVexId(vexId: Int): VexType {
            return lookup.get(vexId)
        }
    }
```

## Util系

```kotlin
fun initLog(tag: String) = Timber.plant(K3ExtTree(tag))
```

## Annotation

```kotlin
@Scope
@kotlin.annotation.Retention
annotation class ActivityScope
```

## Singleton

```kotlin
object MoshiHelper {
    val moshi: Moshi by lazy {
        Moshi.Builder().add(DateAdapter()).build()
    }
}
```

## キャストで括弧少ない

```kotlin
(activityComponent as DebugActivityComponent).inject(this)
```

## キャスト失敗即リターン

```kotlin
val recyclerView = activity.findViewById(R.id.timeline) as? RecyclerView ?: return false
```

## OutputStream

```kotlin
private fun copyAssetFileToCache(context: Context, assetFilePath: String, cacheFileName: String) {
        val cachedModelFile = File(context.cacheDir, cacheFileName)
        if (cachedModelFile.exists()) {
            cachedModelFile.delete()
        }
        FileOutputStream(cachedModelFile).use { outputStream ->
            context.resources.assets.open(assetFilePath).use { inputStream ->
                inputStream.copyTo(outputStream)
            }
        }
    }
```

```kotlin
    contentResolver.query(this.data, arrayOf(MediaStore.Images.Media.DATA), null, null, null).use {
        it.moveToFirst()
        return it.getString(it.getColumnIndexOrThrow(MediaStore.Images.Media.DATA))
    }
```

## get省略

```kotlin
fun layoutInflater(): LayoutInflater = activity.layoutInflater
```

* default値
* if/else -> when
```kotlin
if (mIsAnimating) return
if (dyConsumed > 0) {
    animateHide(child)
} else {
    animateShow(child)
}
```

```kotlin
        when {
            mIsAnimating -> return
            dyConsumed > 0 -> animateHide(child)
            else -> animateShow(child)
        }
```
* data class
* parameter name
* 空メソッド　`fun foo() = Unit`
* 空クラス　`class Foo`
* string template
```kotlin
return originalResponse.newBuilder()
                    .header("Cache-Control", "public, max-age=${60 * 3}")
                    .build()
```
* multiline string template
