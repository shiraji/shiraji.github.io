---
layout: post
title: "What's good about Kotlin in Android development"
date: 2016-12-11 00:00:01 +0900
comments: false
categories: Android Kotlin
keywords: Android Kotlin Rx Databinding
description: describe what's good about choosing Kotlin for Android development in Japanese
---

これは[Kotlin Advent Calendar 2016](http://qiita.com/advent-calendar/2016/kotlin)の12/11の記事です。

<blockquote class="twitter-tweet" data-lang="ja"><p lang="ja" dir="ltr">kotlinでやるとAndroid簡単に書ける手法まとめとかないかな。extentionでbindingadapter書くとか、custom viewのコンストラクタをJvmOverloadsで省略とか。</p>&mdash; しらじ (@shiraj_i) <a href="https://twitter.com/shiraj_i/status/768082977569898496">2016年8月23日</a></blockquote>
<script async src="//platform.twitter.com/widgets.js" charset="utf-8"></script>

8月23日にこんなつぶやきをして、今日まで溜めたAndroid開発をしていて、こう書くと簡単でキモチイイ！！！というKotlinの文法を紹介したいと思います。

(あくまで自分が気持ちいいってだけだからね！)

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

# 自己紹介

[Kotlin 1.0.4](https://blog.jetbrains.com/kotlin/2016/09/kotlin-1-0-4-is-here/), [Kotlin 1.0.5](https://blog.jetbrains.com/kotlin/2016/11/kotlin-1-0-5-is-here/)で名前が載ったExternal Contributorsの一人です(嬉しいから自慢)。主にKotlin Pluginの静的解析にコントリビュートしています。Kotlinで書かれている[PermissionsDispatcher](https://github.com/hotchemi/PermissionsDispatcher)の開発にも参加しています。

Android開発経験3年ほどで、今はAndroidをJavaでもKotlinでも開発しています。

# 前提条件と想定読者

* Android開発はある程度知っている
* KotlinでのAndroid興味がある人
* KotlinでのAndroid開発良いと言われてるけど、何がいいかわからねな人

Kotlinの文法はJavaコードと比較すればだいたいわかる感じで記載していきますが、もしわからなければ、[@shiraj_i](https://twitter.com/shiraj_i)にメンションいただければ答えますので、お気軽に質問して下さい。

# キモチイイ！文法たち

Kotlinは書きやすいとよく耳にしますが、実際どういうところでどういう文法にすると「書きやすい」になるのかJavaとの比較があまりありません。そこで独断と偏見で気持ちいい文法だこれ！と思った文法や書き方を紹介したいと思います。

Kotlinで一番有名であろう機能、Null安全やセミコロンレスに関しては多くのドキュメントやブログがありますので割愛します。

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

あれ？ながｋ...気持ちいいですね！

## getter/setter省略

Kotlinでは、getter/setterがあった場合、propertyとしてアクセス可能になります。AOSPに書いてあるgetter/setterも同様です。

`Activity#getLayoutInflater()`を使うようなメソッドを定義する場合

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

Javaっぽいコードを書くとこのようにワーニングを出してくれるので、都度修正していくとKotlinらしい文法の勉強も捗ります。(platform typeにはご注意下さい)

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

(Kotlinのコンストラクタ自体は気持ちよくないので説明省略します。)

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

使い方もJavaのときと変わりません。

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

これをKotlinでもそのままif/else書けます。

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

## 式

Kotlinではifやwhenなど諸々が式です。

例えば、ifの結果を変数に代入する場合、

```java
int foo;
if(flag) {
    foo = 10;
} else {
    foo = 100;
}
```

kotlinではこんな感じになります。

```kotlin
val foo = if (flag) {
    10
}  else {
    100
}
```

単純な場合、`{}`は省略するので一行で書くことが多いです。

```kotlin
val foo = if (flag) 10 else 100
```

条件により違った値を代入しているのですが`val`で定義出来るのがポイントです。

ちなみに、[PermissionsDispatcher](https://github.com/hotchemi/PermissionsDispatcher)さんでも利用していて、メソッドの引数として使ったりもしています。

```kotlin
builder.beginControlFlow("if (\$N\$T.shouldShowRequestPermissionRationale(\$N, \$N))", if (isPositiveCondition) "" else "!", PERMISSION_UTILS, targetParam, permissionField)
```

ネストさせることも出来るのですが、複雑になるので見にくい場合はローカル変数に切り出すのが良いと思います。

## Annotation

Dagger2を使う場合、ActivityやFragmentスコープを作るために独自Annotation作ったりします。

```java
@Scope
@Retention(RetentionPolicy.RUNTIME)
public @interface ActivityScope {
}
```

`@interface`というキーワードを使っていましたが、Kotlinでは`annotation`と表現します。直感的で良いです。

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

ちょっと手が滑ると

```kotlin
fun Context.showLongToast(@StringRes id: Int) {
    Toast.makeText(this, id, Toast.LENGTH_LONG).show()
}

fun View.onClickFab() {
    context.showLongToast(R.string.message_fab)
}
```

topレベルの拡張メソッドは用法・用量を守って正しくお使い下さい。

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

nestが激しいのでバラしていきます。まずはこの部分。

```kotlin
arguments = Bundle().apply {
    putParcelable(PARCELABLE_KEY, entity)
}
```

これをメソッド化すると

```kotlin
private fun entity(bundle: Bundle, entity: MyPacel): Bundle {
    return bundle.apply {
        putParcelable(PARCELABLE_KEY, entity)
    }
}
```

bundle変数に関する処理なので、Bundleの拡張メソッドと考えると良さ気。

```kotlin
private fun Bundle.entity(entity: MyPacel): Bundle {
    return this.apply {
        putParcelable(PARCELABLE_KEY, entity)
    }
}
```

return文一文なので、省略。thisも必要ないので、省略。

```kotlin
private fun Bundel.entity(entity: MyPacel) = apply { putParcelable(PARCELABLE_KEY, entity) }
```

イマココ

```kotlin
fun newInstance(entity: MyPacel) =
     SimpleDialogFragment().apply {
         arguments = Bundle().entity(entity)
     }

private fun Bundel.entity(entity: MyPacel) = apply { putParcelable(PARCELABLE_KEY, entity) }
```

SimpleDialogFragmentの部分も同じように拡張メソッドを使って書くと。

```kotlin
fun newInstance(entity: Entity) = MyFragment().entity(entity)

private fun MyFragment.entity(entity: Entity) = apply { arguments = Bundle().entity(entity) }

private fun Bundle.entity(entity: Entity) = apply { putParcelable(KEY, entity) }
```

だいぶシンプルになりました。ヨイヨヨイヨ。

## setVisibleOrGone

Databinding前には結構使うことが多かったのですが、今となっては・・・でも一応。

Viewを消してしまうか表示するかをBoolean値で判別するようなUtil系のメソッドを書くとすると

```java
public static void setVisibleOrGone(View view, boolean isVisible) {
    if(view == null) {
        return;
    }

    if(isVisible) {
        view.setVisiblity(View.VISIBLE);
    } else {
        view.setVisiblity(View.GONE);
    }
}
```

これをkotlinで書くと一行でいけます。

```kotlin
fun View?.setVisibleOrGone(isVisible: Boolean) {
    this?.visibility = if (isVisible) View.VISIBLE else View.GONE
}
```

nullableのViewの拡張メソッド、null安全、setter/getterの省略、if式利用と色々Javaにはない機能を利用しています。

## BaseObservable

公式にある、以下のコードをKotlinで気持ちよく書いてみます。

```java
private static class User extends BaseObservable {
   private String firstName;
   private String lastName;
   @Bindable
   public String getFirstName() {
       return this.firstName;
   }
   @Bindable
   public String getLastName() {
       return this.lastName;
   }
   public void setFirstName(String firstName) {
       this.firstName = firstName;
       notifyPropertyChanged(BR.firstName);
   }
   public void setLastName(String lastName) {
       this.lastName = lastName;
       notifyPropertyChanged(BR.lastName);
   }
}
```

Kotlinで書く前に、そもそも具体的にやることは以下

* getterに`@Bindable`をつける
* setterの最後に`notifyPropertyChanged(BR.firstName);`を呼ぶ

```kotlin
class User : BaseObservable {
    @get:Bindable
    var firstName: String
        set(value) {
            field = value
            notifyPropertyChanged(BR.firstName)
        }

    @get:Bindable
    var lastName: String
        set(value) {
            field = value
            notifyPropertyChanged(BR.lastName)
        }
}
```

setter/getterを書かなくて良いのでシンプルになってます。

もうちょいキレイになればいいですが、わざわざメソッド書かなくて良いというだけでも気持ちいいです。

## Delegate

継承するな！移譲しろ！と言われてもJava使ってると継承しちゃいガチなのですが、KotlinのDelegateがあるとこれを積極的に利用したいなと思えるようになります。

ViewModelにRxのSubscriptionの機能を実装し、`Activity#onDestroy`時に`unsubscribe()`したいという時

```java
public class MyViewModel(CompositeSubscription compositeSubscription) {
    // ...
    public void unsubscribe() {
        compositeSubscription.unsubscribe();
    }
}
```

Kotlinでdelegateすると`unsubscribe()`は書かなくて良くなります。

```kotlin
class MyViewModel @Inject constructor(var compositeSubscription: CompositeSubscription) : Subscription by compositeSubscription {
    // ...
}
```

Activityではこんな感じ。`MyViewModel`で実装していないのに`unsubscribe()`を呼ぶことが出来る。

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

droidkaigi2016の[ArrayRecyclerAdapter](https://github.com/konifar/droidkaigi2016/blob/master/app/src/main/java/io/github/droidkaigi/confsched/widget/ArrayRecyclerAdapter.java)を以下のようなにすることもできるけど、余計なメソッドも生えるので、用法と用量を(省略

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

## 遅延初期化

Javaで簡単にやる場合、getterを作成し、propertyアクセスは禁止し、そのgetter経由で値を取得するというルールの下かろうじて出来る遅延初期化処理です。

例えば、Databindingの`setContentView`を利用して、`binding`変数を初期化します。

```java
private MyBinding binding;

private MyBinding getBinding() {
    if(binding == null) {
        binding = DataBindingUtil.setContentView<MyBinding>(this, R.layout.my);
    }
    return binding;
}
```

Kotlinでは`by lazy`を利用して書きます。一旦`binding`変数にアクセスしたら`by lazy`内の処理が実行され、初期化されます。

```kotlin
private val binding by lazy {
    DataBindingUtil.setContentView<MyBinding>(this, R.layout.my)
}
```

valであるのも良いです。

ちなみに、enumに`ordinal`ではない数値のidを振ることがあって、そのidから逆引きしたい時にキャッシュした`SparseArray`を利用して逆引きしますが、この時も`by lazy`使います。

```kotlin
companion object {
    private val lookup: SparseArray<MyType> by lazy {
        SparseArray<MyType>().apply {
            EnumSet.allOf(MyType::class.java).forEach {
                this@apply.put(it.typeId, it)
            }
        }
    }

    fun fromTypeId(typeId: Int): MyType {
        return lookup.get(typeId)
    }
}
```

## parameter name

Javaではそもそも出来ないのですが、こんなPOJOクラスがあったとします。

```java
public class User {
    private String firstName;
    private String lastName;

    public User(firstName, lastName) {
        this.firstName = firstName;
        this.lastName = lastName;
    }

    // その他メソッド省略
}
```

```java
new User("名", "姓");
new User("姓", "名"); // 間違っているけど、コンパイルOK
```

第一引数と第二引数がfirstなのか、lastなのかわからなくなります。ランタイムでしか検知できないのが辛いです。

```kotlin
User(firstName = "名", lastName = "姓")
```

引数に名前を設定して、メソッドを呼び出せます。こんなことしても叱られませんし、間違った挙動を起こしません。同じような型が多いメソッド呼び出しでは積極的に使うと気持ち良いです。

```kotlin
User(lastName = "姓", firstName = "名")
```

## 最後に

リストアップしてみたら結構出てきました。。。

長すぎたので、落選した項目は[gist](https://gist.github.com/shiraji/2caf8190d282ab3594a21b467980267e)で公開しています。
