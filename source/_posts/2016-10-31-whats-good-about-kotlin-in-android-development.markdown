---
layout: post
title: "What's good about Kotlin in Android development"
date: 2016-10-31 13:56:43 +0900
comments: false
categories: Android Kotlin
keywords: Android Kotlin Rx Databinding
description: describe what's good about choosing Kotlin for Android development in Japanese
---

Kotlinの文法紹介というより、Javaで書いてた辛い文法をこんな感じでKotlinで書くこと出来るよ！紹介です。これが最強だ！と言ってるわけではなく、自分がこう書くとキモチイイ！だけです。(という予防線を張っておこう・・・)

# 自己紹介

[Kotlin 1.0.4](https://blog.jetbrains.com/kotlin/2016/09/kotlin-1-0-4-is-here/)のExternal Contributorsの一人です。主にKotlin Pluginの静的解析にコントリビュートしています。

AndroidもKotlinで開発しています。

# 前提条件と想定読者

* Android開発はある程度知っている
* KotlinでのAndroid興味がある人
* KotlinでのAndroid開発良いと言われてるけど、何がいいかわからねな人

Kotlinの文法はJavaコードと比較すればだいたいわかる感じで記載していきますが、もしわからなければ、[@shiraj_i](https://twitter.com/shiraj_i)にメンションいただければ答えますので、お気軽に質問して下さい。

# キモチイイ！文法たち

Kotlinは書きやすいとよく耳にしますが、実際どういうところでどういう文法にすると「書きやすい」になるのかJavaとの比較があまりありません。そこで独断と偏見で気持ちいい文法だこれ！と思った文法や書き方を紹介したいと思います。

Kotlinで一番有名であろう、Null安全やセミコロンレスに関しては多くのドキュメントやブログがありますので割愛します。

## 一行メソッド

ある特定のテキストを返すだけのメソッドを作る時、Javaで書くとこんな感じになります。

```java
public String getName() {
  return "MyApp";
}
```

Kotlinでも同じように書けます。

```kotlin
fun getName(): String {
  return "MyApp"
}
```

ただ、Kotlinは一行でreturn出来る場合、`=`をつけて`{}`を省略することが出来ます。

```kotlin
fun getName(): String = "MyApp"
```

さらに、戻り値の型が明らかな場合、型の指定しなくても良いので

```kotlin
fun getName() = "MyApp"
```

短くてだいぶ気持ちいいですね。

こんな感じで、以下もJavaの例文を出して、Kotlinで気持ち良くなっていきます。それではどんどんいきます。

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

Kotlinはnull時にこれをしてくれという`?:`文法が用意されています。それを使うと一行で書けちゃいます。

```kotlin
fun foo(text: String?) {
    text ?: return
    // ...
}
```

null時に別値を代入ということも可能です。

```kotlin
fun foo(text: String?) {
    val bar = text ?: "" // textをbarに代入する。textがnullだった場合、空文字とする。
    // ...
}
```

## 空クラス

Javaでは空クラスだろうと、`{}`を書かなくてはなりません。特に目印用のinterfaceとかであると思いますが

```java
interface Foo {}
```

Kotlinではボディが空のクラスの場合、`{}`を書かなくて良いので

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

## getter/setter省略

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

![getter_setter](https://raw.githubusercontent.com/wiki/shiraji/images/blog/images/whats-good-about-kotlin-in-android-development/getter_setter.png)

Javaっぽいコードを書くとこのようにワーニングを出してくれるので、都度修正していくとKotlinらしい文法の勉強も捗ります。

## パラメータのデフォルト値

ここのパラメータだいたい同じ値なのだけど、時々違うから、overloadメソッドを用意するか！ってことありませんか？

```java
  public static boolean maybeStartActivity(Context context, Intent intent) {
    return maybeStartActivity(context, intent, false);
  }

  private static boolean maybeStartActivity(Context context, Intent intent, boolean chooser) {
      // ...
  }
```

かの有名な[u2020](https://github.com/JakeWharton/u2020/blob/70dd9572f45afb21a62ff414d19b7c095d737372/src/main/java/com/jakewharton/u2020/util/Intents.java)にもありました。

Kotlinはパラメータのデフォルト値を定義出来ます。

```kotlin
  fun maybeStartActivity(context: Context, intent: Intent, chooser: Boolean = false): Boolean {
      // ...
  }
```

### カスタムViewのコンストラクタ

パラメータのデフォルト値に関連して、カスタムViewのコンストラクタの定義って大変だと思います。

```java
    public CustomView(Context context) {
        this(context, null);
    }

    public CustomView(Context context, AttributeSet attrs) {
        this(context, attrs, 0);
    }

    public CustomView(Context context, AttributeSet attrs, int defStyleAttr) {
        super(context, attrs, defStyleAttr);
        binding = DataBindingUtil.inflate(LayoutInflater.from(context), R.layout.custom_view, this, true);
    }
```

Kotlinはデフォルト値を定義したメソッドを上記のようにJavaから見たら複数あるようにする`@JvmOverloads`というアノテーションがあります。

これを使うと、カスタムViewのコンストラクタは一行定義するだけで書けます。

```kotlin
class @JvmOverloads CustomView(context: Context, attrs: AttributeSet = null, defStyleAttr: Int = 0) {
}
```

(Kotlinのコンストラクタ自体は気持ちよくないので省略します。)

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

## Stringテンプレート

JavaでStringの結合をする場合、こんな感じになります。

```java
return originalResponse.newBuilder()
                    .header("Cache-Control", "public, max-age=" + 60 * 3)
                    .build();
```

KotlinにはStringテンプレートとしてString内に`${}`で変数を書くことが出来ます。

```kotlin
return originalResponse.newBuilder()
                    .header("Cache-Control", "public, max-age=${60 * 3}")
                    .build()
```

変数一つだけである場合、`{}`の省略も出来ます。

```kotlin
val foo = 1
val bar = "Text$foo" // <= "Text1"という文字列に
```

## 複数行のString

JavaのStringで複数行を生成する場合、結構辛いです。

```java
String text = "aaa\nbbb\nccc";
```

Kotlinでは`"""`を使うことで複数行のStringの定義を出来ます。

```kotlin
val text = """aaa
              bbb
              ccc""".trimMargin();
```

.trimMargin()のこととか、マージンの開始位置とか複数行のStringは結構多機能ですが、詳細知りたければ[公式のstring-literals](https://kotlinlang.org/docs/reference/basic-types.html#string-literals)を確認してね。

## 複数if -> when

Javaではifが複数ある場合、ちょっとつらいです。

例えば以下のようなコードがあったとします。(Javaでbehaviorを独自実装したときに使っていたコード)

```java
if (isAnimating) return;
if (consumed > 0) {
    hide(child);
} else {
    show(child);
}
```

これをKotlinだとそのまま書けます。

```kotlin
if (isAnimating) return
if (consumed > 0) {
    hide(child)
} else {
    show(child)
}
```

しかし、もう少し簡単にwhenでまとめることも可能です。

```kotlin
when {
    isAnimating -> return
    consumed > 0 -> animateHide(child)
    else -> animateShow(child)
}
```

whenはJavaでいうSwitch文に近いですが、上記のようにwhenの後に条件を付けなかったり、変数の型のcase文に出来たりとめちゃくちゃ気持ちよくなれます。

## Annotation

Dagger2を使う場合、ActivityやFragmentスコープを作るためにカスタムのAnnotation作ったりします。

```java
@Scope
@Retention(RetentionPolicy.RUNTIME)
public @interface ActivityScope {
}
```

`@interface`というキーワードを使っていましたが、Kotlinでは`annotation`と表現します。

```kotlin
@Scope
@Retention
annotation class ActivityScope
```

`Retention`のデフォルト値が`RetentionPolicy.RUNTIME`なので、指定を省略出来るのもスッキリしていて気持ち良いです。

## Singleton

Javaでは(簡易的な)シングルトンを作成する場合、以下のように書く必要がありました。

```java
public class MoshiUtil {
    private static Moshi moshi;

    public static Moshi getMoshi() {
        if (moshi == null) {
            moshi = Moshi.Builder().add(DateAdapter()).build();
        }
        return moshi;
    }
}
```

Kotlinでは、`object`として定義すればアプリ内でシングルトンとして利用可能です。

```kotlin
object MoshiUtil {
    val moshi: Moshi by lazy {
        Moshi.Builder().add(DateAdapter()).build()
    }
}
```

使い方もJavaの時と同じです。

```kotlin
MoshiUtil.moshi.adapter(BlackjackHand::java.class)
```

## rx.ObservableのThread指定方法

ちょこっとしたことなのですが、Rxの実行スレッド方法の指定も気持ちよく書けるようになります。

```java
load()
  .subscribeOn(AndroidSchedulers.mainThread())
  .observeOn(AndroidSchedulers.mainThread())
  .subscribe();
```

Kotlinの拡張プロパティを利用し、スレッドの指定方法を定義します。

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

この拡張プロパティを利用すると以下のように書けます。

```kotlin
load()
  .subscribeOnIO
  .observeOnIO
  .subscribe()
```

## DatabindingのBindingAdapter指定方法

次は拡張メソッドを活用してみます。

DatabindingのBindingAdapterの[公式ドキュメントのコード](https://developer.android.com/reference/android/databinding/BindingAdapter.html)をKotlinで書いてみます。

```java
@BindingAdapter("android:bufferType")
public static void setBufferType(TextView view, TextView.BufferType bufferType) {
    view.setText(view.getText(), bufferType);
}
```

これがこんな感じになります。`view`が消えました。

```java
@BindingAdapter("android:bufferType")
fun TextView.setBufferType(TextView.BufferType bufferType) {
    setText(getText(), bufferType)
}
```

## DatabindingのonClickなどのイベント指定方法

これも拡張メソッドでいきます。

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

```java
fun onClickFab(View view) {
    Toast.makeText(view.getContext(), R.string.message_fab, Toast.LENGTH_LONG).show();
}
```

kotlinではこんな感じ。Toast表示用の拡張メソッドも定義しています。

```kotlin
fun View.onClickFab() {
    Toast.makeText(context, R.string.message_fab, Toast.LENGTH_LONG).show()
}
```

ちょっとやりすぎると

```kotlin
fun Context.showLongToast(@StringRes id: Int) {
    Toast.makeText(this, id, Toast.LENGTH_LONG).show()
}

fun View.onClickFab() {
    context.showLongToast(R.string.message_fab)
}
```

拡張メソッドは用法・用量を守って正しくお使い下さい。

## createIntent/newInstance

自分はcreateIntent/newInstanceパターン大好きです。

これも気持ちよくなれます。

```java
public static Intent createIntent(Context context) {
    Intent intent = new Intent(context, MyActivity.class);
    return intent;
}
```

applyを使って、一行に。

```kotlin
companion object {
    fun createIntent(context: Context) = Intent(context, MyActivity::class.java).apply { }
}
```

newInstanceもやってみます。

```java
public static SimpleDialogFragment newInstance(MyPacel entity) {
    SimpleDialogFragment fragment = new SimpleDialogFragment();
    Bundle bundle = new Bundle();
    bundle.putParcelable(PARCELABLE_KEY, entity);
    fragment.setArguments(bundle);
    return fragment;
}
```

パラメータセットしたインスタンスを作りたいだけなのですが、結構冗長的で辛い。。。

Kotlinで短くしてみます。

```kotlin
 fun newInstance(entity: MyPacel) =
      SimpleDialogFragment().apply {
          arguments = Bundle().apply {
              putParcelable(PARCELABLE_KEY, entity)
          }
      }
```

nestが激しいので拡張メソッドを利用してバラしちゃいます。

```kotlin
fun newInstance(entity: Entity) = MyFragment().entity(entity)

private fun MyFragment.entity(entity: Entity) = apply { arguments = Bundle().entity(entity) }

private fun Bundle.entity(entity: Entity) = apply { putParcelable(KEY, entity) }
```

ヨイですね。

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

by lazy

```kotlin

abstract class MutableListRecyclerAdapter<T, VH : RecyclerView.ViewHolder>(private val list: MutableList<T>) :
        RecyclerView.Adapter<VH>(), Iterable<T>, MutableList<T> by list {

    var itemClickListener: View.OnClickListener? = null

    override fun getItemCount() = list.size

    @UiThread fun addAllWithNotification(items: Collection<T>) {
        val position = itemCount
        addAll(items)
        notifyItemChanged(position)
    }

    @UiThread fun reset(items: Collection<T>) {
        clear()
        addAll(items)
        notifyDataSetChanged()
    }
}

```

こんなこともできるけど、余計なメソッドも生えるので、用法と用量を守ってお使い下さい。

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

* data class
* parameter name
* if式

builder.beginControlFlow("if (\$N\$T.shouldShowRequestPermissionRationale(\$N, \$N))", if (isPositiveCondition) "" else "!", PERMISSION_UTILS, targetParam, permissionField)

* for-loop (index - 1)

for (i in 0 until tabBindings.size()) {

SparseArrayとか

* 日本語使える。

testメソッド名とかで

fun `クレジットカード決済`() {
}

