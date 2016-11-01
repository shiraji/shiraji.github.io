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

Android開発もちょくちょくしており、絶賛Kotlinで開発中です。

# 前提条件と想定読者

* Android開発はある程度知っている
* KotlinでのAndroid興味がある人
* KotlinでのAndroid開発良いと言われてるけど、何がいいかわからねな人

Kotlinの文法はJavaコードと比較すればだいたいわかる感じで記載していきますが、もしわからなければ、[@shiraj_i](https://twitter.com/shiraj_i)にメンションいただければ答えますので、お気軽に質問して下さい。

# キモチイイ！文法たち

Kotlinは書きやすいとよく耳にしますが、実際どういうところでどういう文法にすると「書きやすい」になるのかJavaとの比較があまりありません。そこで独断と偏見で気持ちいい文法だこれ！と思った文法や書き方を紹介したいと思います。

Null安全やセミコロンレスに関しては多くのドキュメントやブログがありますので省略します。

## 一行メソッド

例えば、そのある特定のテキストを返すだけのメソッドがあるとします。Javaで書くとこんな感じになります。

```java
public String getName() {
  return "MyApp";
}
```

これをKotlinだと同じように書けます。

```kotlin
fun getName(): String {
  return "MyApp"
}
```

ただ、Kotlinは一行でreturn出来る場合、`=`をつけて`{}`を省略することが出来ます。

```kotlin
fun getName(): String = "MyApp"
```

さらに、戻りの型が明らかな場合、型の指定しなくても良いので

```kotlin
fun getName() = "MyApp"
```

短いですね！

## null時何する？

例えば、パラメータがnullだった場合、即メソッドを抜けるという処理を書くとします。Javaの場合、結構色々書かなきゃいけません。

```java
public void foo(@Nullable String text) {
    if (text == null) {
        return;
    }
    // ...
}
```

Kotlinはnull時にこれをしてくれという`?:`文法が用意されています。

```kotlin
fun foo(text: String?) {
    text ?: return
    // ...
}
```

null時に別値を代入ということも可能です。

```kotlin
fun foo(text: String?) {
    // textをbarに代入する。textがnullだった場合、空文字とする。
    val bar = text ?: ""
    // ...
}
```

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

## 空メソッド

空メソッド。Javaの場合、`{}`を書かなくてはなりません。

```java
public void foo() {
}
```

Kotlinの場合、一行メソッドと同じように書けます。

```kotlin
fun foo() = Unit
```

あれ？ながｋ(省略

気持ちいいですね！

## get/set省略

Kotlinでは、getter/setterがあった場合、propertyとしてアクセス可能になります。AOSPに書いてあるgetter/setterも同様です。

Activity#getLayoutInflater()を使うようなメソッドを定義する場合

```java
public LayoutInflater getLayoutInflater() {
    return activity.getLayoutInflater();
}
```

Kotlinで書くと以下のように書けます。

```kotlin
fun layoutInflater(): LayoutInflater = activity.layoutInflater
```

実際にはActivity内に`layoutInflater`というプロパティは存在していませんが、Kotlinが解釈してくれます。

もちろんですが、以下のようにも書けます。

```kotlin
fun layoutInflater(): LayoutInflater = activity.getLayoutInflater()
```

ただ、Android Studioさんが「これプロパティアクセスに変えな？」というサジェストが出ます。

**画像**

Javaっぽいコードを書くとこのようにワーニングを出してくれるので、都度修正していくとKotlinらしい文法の勉強も捗ります。

## キャストで括弧少ない

Javaでキャストする場合、括弧が多くなりがちです。PagerAdapterの`destroyItem`を実装してみます。

```java
＠Override public void destroyItem(ViewGroup container, int position, Object obj) {
    ((ViewPager) container).removeView((View) obj)
}
```

Kotlinだと`as`を使ってキャストします。文法的にも括弧が減り、どこで括弧が終わっているのかがわかりやすくなります。

```kotlin
override fun destroyItem(container: ViewGroup?, position: Int, obj: Any?) {
    (container as ViewPager).removeView(obj as View)
}
```

### キャスト失敗時に何する？

null時に何する？との組み合わせることでキャスト失敗時に何するかの定義も簡単にできます。

```kotlin
override fun destroyItem(container: ViewGroup?, position: Int, obj: Any?) {
    container as? ViewPager ?: return
    // ...
}
```

また、readonlyの変数のキャストに成功すると自動的にその変数をキャストしてくれます。メソッドパラメータはreadonly。各所に出てくる`val`と定義されている変数もreadonlyです。(余談ですが、immutableではありませんので注意して下さい。)

```kotlin
override fun destroyItem(container: ViewGroup?, position: Int, obj: Any?) {
    container as? ViewPager ?: return
    obj as? View ?: return
    container.removeView(obj)
}
```

mutable変数`var`の場合、`as?`後に変更可能なので、自動的にキャストしてもらえないので注意。Kotlinでは理由がない限り、`var`を使わないほうが良いです。

## Util系

XxxUtilsとか作って、全メソッドをstaticにして、privateコンストラクタを作って・・・みたいなやり方をJavaではちょくちょくしていました。

```java
public class LogUtil {
  private LogUtil() {}

  public void initLog(String tag) {
    Timber.plant(ExtTree(tag))
  }
}
```

kotlinではTopレベルにメソッドを書けば、このようなUtil系のメソッドを書くことが出来ます。

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












クラスのstaticメソッドは後述します。

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

## get省略

```kotlin
fun layoutInflater(): LayoutInflater = activity.layoutInflater
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
* string template
```kotlin
return originalResponse.newBuilder()
                    .header("Cache-Control", "public, max-age=${60 * 3}")
                    .build()
```
* multiline string template
