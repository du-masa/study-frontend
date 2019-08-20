# パフォーマンスチューニング

## Loadingにおけるパフォーマンスチューニング

Loadingは、リソースをダンロードしそのリソースを解析するフェーズです。
なるべく少ない時間でリソースをダウンロードすることや、ダウンロードをブロックするような処理を避けることが必要になります。

Loadingにおけるチューニングの方針をまとめると下記になります。

- 読み込むリソースの大きさや数を減らす
- ダウンロードやレンダリングをブロックする読み込みや処理を減らす
- ブラウザとサーバー間の遅延を減らす
- ブラウザキャッシュを有効に使う

### リソースの縮小化、最適化

HTML, CSS, JSの縮小化(圧縮)や画像の最適化は可能な限り行うべきでしょう。

縮小化はnpmのモジュールを使って自動化すると便利です。
googleでは下記モジュールの使用をオススメしています。

- HTML [html-minifier](https://github.com/kangax/html-minifier)
- CSS [cssnano](https://github.com/cssnano/cssnano) [csso](https://github.com/css/csso)
- JS [UglifyJS2](https://github.com/mishoo/UglifyJS2)

参考: [リソース（HTML、CSS、JavaScript）を圧縮する](https://developers.google.com/speed/docs/insights/MinifyResources?hl=ja)


また、webpackではこれらのモジュールを内包したwebpack用のプラグインが用意されているのでそちらを使うと良いでしょう。

- HTML [html-webpack-plugin](https://github.com/jantimon/html-webpack-plugin)
- CSS [optimize-css-assets-webpack-plugin](https://github.com/NMFR/optimize-css-assets-webpack-plugin)
- JS [uglifyjs-webpack-plugin] (https://github.com/webpack-contrib/uglifyjs-webpack-plugin)


画像の最適化は、画像の余計な情報を削ぎ落とし軽量化します。
最適化が必要な主な画像のJPEG, PNG, GIFのそれぞれにモジュールが用意されていますが、
[imagemin](https://github.com/imagemin/imagemin) などのそれらのモジュールをラッピングしたモジュールを使うと便利です。

webpackでも[imagemin-webpack](https://www.npmjs.com/package/imagemin-webpack)や[imagemin-webpack-plugin](https://www.npmjs.com/package/imagemin-webpack-plugin)などのツールが用意されています。

また、GUIアプリの[ImageOptim](https://imageoptim.softonic.jp/mac)やオンラインツールの[tinypng](https://tinypng.com/)もあります。

### JavaScriptの同期的な読み込みを避ける

LoadingフェーズでHTMLのパースを行っている途中でJavaScriptの読み込みを行うと、パースや他のリソースの読み込みもブロックします。これらはJavaScriptを読み込み実行が終わるまでブロックします。

例:
```html
<!doctype html>
<html>
  <head>
    <script src="script.js"></script>
    <link href="style.css" rel="stylesheet" />
  </head>
  <body>
  .
  .
  .
</html>
```

これを避けるには、JavaScriptの読み込みは、bodyタグの最後に書くか非同期で読み込む必要があります。非同期読み込みは、`defer`属性か`async`属性を使って行うのが良いでしょう。


#### defer
　
```html
  <script src="script.js" defer></script>
```

JavaScriptの読み込みを非同期で行います。HTMLがパースされるとsrc属性のファイルのダウンロードは行われますが、HTMLのパースをブロックすることはありません。

そして、JavaScriptの実行は**DOMツリーが構築されて初めて実行**されます。
また、`defer`属性が設定されたファイルの実行順は**記述順が保証**されます。


```html
  <script src="foo.js" defer></script>
  <script src="bar.js" defer></script>
```

上記の例では`foo.js`が実行されて`bar.js`が次に実行されます。


#### async

```html
  <script src="script.js" async></script>
```

`defer`属性と同じように非同期で読み込みを行います。`defer`属性と違うのは**実行タイミングが保証されていない**点です。ファイルが読み込まれたタイミングで実行されます。そのためDOM構築を待ちません。DOM操作があるファイルは注意が必要です。

また、複数のJavaScriptファイルを読み込んだ場合も、実行順が保証されていません。
そのため、ライブラリ依存のあるファイルの場合はライブラリが未実行だった場合にエラーになる可能性があります。

```html
  <script src="foo.js" async></script>
  <script src="bar.js" async></script>
```

`foo.js`と`bar.js`は読み込まれた順で実行されます。

`async`属性を使ってライブラリの読み込み順を保証する場合は、ライブラリもまとめた１つのバンドルファイルを用意するといった工夫が必要です。

#### defer vs async

`async`属性は非同期で読み込み次第実行されるため、良いパフォーマンスが出ます。しかしDOM構築などの依存がある場合は、`defer`属性を使った読み込みに切り替えると良いでしょう。


### ピクセル比ごとに読み込む画像分ける

Retinaディスプレイやレスポンシブデザインに対応すると、同じ画像で解像度の違う画像を複数用意したりします。よく行うのはCSSのメディアクエリで表示非表示を出し分けたりします。

```html
  <img src="sp.png" class="spImage">
  <img src="pc.png" class="pcImage">
```

```css

.spImage {
  dispaly: block;
}

.pcImage {
  dispaly: none;
}

@media (min-width: 376px) {
  .spImage {
    dispaly: none;
  }

  .pcImage {
    dispaly: block;
  }
}
```

これだと、２つの画像をLoading時に読み込んでしまいます。出し分け画像が多くなればなるほど１度にダウンロードするファイルがその分増えてしまいます。

imgタグの`srcset`属性指定すると画面サイズやデバイスピクセル比に合わせた画像を読み込むことができます。

```html
<img src="pc.png" srcset="pc.png 1440w, sp.png 375w">
```

上記のように指定すると、viewportが375px以下の場合はsp.pngが1440px以下の場合はpc.pngが表示されます。また、`srcset`属性はデバイスピクセル比を自動で調節してくれるので、2倍のRetinaディスプレイの場合は、740pxまでが`sp.png`を表示します。

この`srcset`がパフォーマンス的に良いとされるのは、ビューポートを見て`pc.png` と `sp.png` どちらかしかダウンロードしないようになっています。

`srcset`を使った設定はこちらで詳しく解説しています。[srcsetとsizes属性でサイズ(解像度)ごとに画像を出し分ける方法](https://junzou-marketing.com/srcset-sizes-attributes)

### webフォントの最適化

webフォントを読み込んで使う場合、配布されているものをそのまま使うと、特に日本語などは多くのフォントを使用するためファイルサイズが大きくなってしまいます。

その場合は、必要な文字を絞った(例えば常用漢字のみ)サブセット化がオススメされます。

サブセット化は下記のサイトを参考にすると簡単に行えます。

[日本語Webフォントをサブセット化して軽量化する方法](https://heysho.com/webfont/)


また、webフォントはダウンロードされるまで、レンダリングされたテキストに適用されません。
その間のテキストの扱うを指定できるCSSプロパティがあります。

webフォントはCSSの`@font-face`を使って利用設定しますが、その設定に`font-display`プロパティを設定します。

```css
@font-face {
  font-display: swap;
}
```

`font-display: swap;` を指定すると、webフォントが利用可能になるまで他のシステムフォントでテキストが表示されるようになります。`font-display: auto;` を指定することも可能ですが、その場合はwebフォントが利用可能になるまでテキストは表示されません。アクセシビリティを考えると`font-display: swap;` を指定する方が良いでしょう。


### リソースの事前読み込み

resouce hintsと呼ばれる機能を使うことで今後必要になるリソースを事前読み込みすることができます。

resouce hintsには４つの指定方法があります。link要素のref属性に下記いずれかを指定します。

- dns-prefetch
- preconnect
- prefetch
- prerender

#### dns-prefetch

`dns-prefetch`の設定をすると、バックグランドでDNSの名前解決を行い、解決するとブラウザのキャッシュに格納されます。CDNなどでDNSの名前解決が必要になる場合に使うと有効です。

```html
<link ref="dns-prefeach" href="https://example.com">
```

#### preconnect

`preconnect`を設定をすると、TCPのハンドシェイク、TLSのコネクションの解決を行ってくれます。

```html
<link ref="preconnect" href="https://example.com">
```


#### prefetch

`prefetch`を指定すると、指定されたリソースを事前読み込みして、ブラウザにキャッシュされます。今後のページで使用が予想されるリソースを事前に読み込んでおくことが可能です。

```html
<link ref="prefetch" href="image.jpg" as="image">
```

`href`属性にはリソースのパスを、`as`属性にはリソースの種類を指定します。
`as`属性に指定できる内容は下記にまとまっています。

https://w3c.github.io/preload/#as-attribute

#### prerender

`prerender`を指定すると、レンダリングの処理まで事前に行うことができます。そのためHTMLの指定も可能です。リンク先のHTMLなどを指定しおくと遷移したタイミングでレンダリングまで終わっているのでユーザーを待たせません。

しかし、指定するページが多いとネットワーク帯域を圧迫したりCPUの処理に負荷がかかり現在表示しているページにまで影響を与えかねないので大きすぎるリソースには利用などの制約が必要です。


#### preload

resouce hintsと似たもので`preload`と呼ばれるものがあります。`link`要素に`preload`を指定して、そこに記述されたリソースは優先的に取得されるようになります。

優先的にというのは、通常通りのリソースダウンロード順で読み込まれるリソースよりも先に読み込むということです。

```html
<link rel="preload" href=“sp.jpg” as="image">
``` 

ファーストビューで表示される画像などを優先的に読み込みたい場合に有効です。

また、メディアクエリを使った指定もできるのでスマホサイズのビューポートだったら優先的に読み込むといったことも可能です。

```html
<link rel="preload" href=“sp.jpg” as="image" media="(max-width: 600px)">
```

#### resouce hintsとpreloadの違い

resouce hintsは**今後読み込まれるであろうページやリソースをバックグラウンドで取得**し、**いざ必要になった時に素早く表示させること**ができるものです。preloadは**今必要なリソースのうち、優先度を高く読み込みたいもの**を指定できるものです。


resouce hintsやpreloadの検証については下記記事にもまとまっていますのでご確認ください。

[resouce hintsとpreloadを使ってリソースの取得を最適化する](https://blog.kazu69.net/2016/03/19/optimize_resources_using_resoucehint_and_preload/)


### ブラウザキャッシュの利用

ブラウザキャッシュの設定はいくつかあります、そのうち強いキャッシュと弱いキャッシュに分けられます。

- Expiresヘッダー
- Cache-Controlヘッダー
- Last-Modifiedヘッダー
- ETagヘッター

上記のうち、`Expiresヘッダー`と`Cache-Controlヘッダー`は強いキャッシュ、`Last-Modifiedヘッダー`と`ETagヘッター` は弱いキャッシュです。

強いキャッシュは、キャッシュの期限が決まっていてその間リソースが変更されていてもキャッシュされたリソースの内容を読み続けます(キャッシュが有効の間はHTTP通信を行わない)。

弱いキャッシュは、HTTP通信を行いますがリソースに変更がない場合は304レスポンスを返し、ブラウザキャッシュを使うようにします。変更があった場合は新しいリソースを返します。

これらの設定はwebサーバー側で行います。

#### Expiresヘッダー

Expiresヘッダーは期限を日付で指定して、その期限が切れるまでキャッシュとして保存します。

Expiresヘッダーの指定がされている場合は、レスポンスヘッダーの`Expires`の項目にキャッシュが有効な日付が設定されています。

```
Expires: Mon, 02 Nov 2015 13:19:30 GMT
```

Expiresヘッダーは日付指定を行っているため、サーバー時間とクライアント時間がずれている場合は意図しないキャッシュになる恐れがあります。

#### Cache-Controlヘッダー

Cache-Controlヘッダーはキャッシュの期限を秒数で指定します。

```
Cache-Control: max-age=600
```

上記の例では600秒間ブラウザにキャッシュします。


#### Expiresヘッダー と Cache-Controlヘッダー

ExpiresヘッダーとCache-Controlヘッダーの両方が指定されている場合は、Cache-Controlヘッダーが優先せれます。Cache-Controlヘッダーの方が後発の機能で、前途したタイムゾーンの問題も踏まえてもCache-Controlヘッダーを使うのが良いでしょう。

Cache-Controlヘッダーはモダンブラウザにも全て対応しているため、基本的にはCache-Controlヘッダーを使うのが良いと思います。

#### Last-Modifiedヘッダー

Last-Modifiedヘッダーはリソースがいつ更新されたのかを設定します。

webブラウザはLast-Modifiedヘッダーに最終更新日を設定してレスポンスに返します。

```
Last-Modified: Mon, 02 Nov 2015 13:19:30 GMT
```

その次にブラウザが同じURにクエストする時に `If-Modified-Since` ヘッターを送ります。

```
If--Modified-Since: Mon, 02 Nov 2015 13:19:30 GMT
```

この日付と比べてサーバーのリソースの`Last-Modified`が後だった場合は、新しいリソースを返します。そうでない場合は304レスポンスを返します。

#### ETagヘッダー

ETagヘッダーは一意のIDを設定します。

```
ETag: "DAFJCEK132amdDFEJGVNDE01DAK1D"
```

このETagをブラウザは保存して、次同じURLにアクセスする際に`If-None-Match`ヘッターに同じIDを設定します。


```
If-None-Match: "DAFJCEK132amdDFEJGVNDE01DAK1D"
```

サーバーはこのIDが違った場合(リソースの変更があった場合は違う)のみ新しいリソースを返すようにしています。


#### Last-ModifiedヘッダーとETagヘッダー

Last-ModifiedヘッダーとETagヘッダーを両方指定した場合は、ETagヘッダーが優先されます。


#### 強いキャシュと弱いキャッシュ

強いキャッシュはあまり変更のないものに使用すると有効です。静的な画像とは良いかもしれません。
弱いキャッシュは頻繁に変更があるHTMLなどに設定すると良いでしょう。


## Scriptingにおけるパフォーマンスチューニング


### ページ非表示時の処理を減らす

ブラウザで複数タブを開いていて、非表示時でもJavaScriptの処理がされていてメモリやCPUに負荷がかかることがあります。

そんな時は、ページの表示状態を確認して非表示時は処理を止めるといった対策も考えられます。

ページが表示状態かどうかを確認するには、`Page Visibility`APIがあります。

`document.hidden` でページが表示されている状態かどうかを確認できます。

```js
console.log(document.hidden);
```

また、`document.visibilityState`で3つの表示状態を取得できます。

- visible 表示状態
- hidden 非表示状態
- prerendering link要素のprerenderingによるプレレンダリングがされて、かつユーザーから見えていない状態


さらに、ページの可視状態が変わった時のイベントも用意されています。

```js
document.addEventListener('visibilitychange', () => {
  if(document.visibilityState === 'visible') {
    console.log('表示状態');
  } else if (document.visibilityState === 'prerendering') {
    console.log('プレレンダリング状態');
  } else if (document.visibilityState === 'hidden') {
    console.log('非表示');
  }
});
```

Page Visibility APIは、例えば動画サービスなどでページが非表示になったら自動的止めるなどどいった仕様で使えます。


### 要素の交差を検知する

スクロールに応じてアニメーションをさせるといった処理はよくありますが、その時に使用するスクロールイベントは頻繁発生するためパフォーマンス上の問題があります。また、body要素のスクロール位置を毎回取得する行為も再レンダリングを引き起こす要因となります。

その問題の解決手段として`Intersection Observer`APIがあります。Intersection ObserverはあるDOM要素とそのDOM要素の親要素が視覚的に交差したかどうかを監視するAPIです。


```html
<html>
<style>
body {
 padding: 1000px 0 200px;
}

#traget {
  width: 100px;
  height: 100px;
  background: red;
}
</style>
<body>
  <div id="traget">
    target
  </div>
</body>
</html>
```

テスト用に上記のHTMLで確認してみます。`<div id="traget">` が親であるbodyに視覚的に交差したかどうかをみてみます。


```js

window.addEventListener("DOMContentLoaded", () => {
  // インスタンス作成。コールバック関数は監視対象となる要素が視覚的に見えたり、消えたかどうかで実行される
  const observer = new IntersectionObserver((snapshot) => {
    console.log(snapshot);
  });

  // 監視対象の要素を取得
  const target = document.querySelector("#target");

  // 監視対象を設定
  observer.observe(target);
});
```


<img src="/image/performance/intersection.gif" width="700">

上記のコードでは、`<div id="traget">` が表示・非表示されるたびに`IntersectionObserver` のコールバック関数が呼ばれています。

引数は、配列になっていてその中に監視結果のオブジェクトが入っています。

オブジェクト主な中身は下記です。

- time タイムスタンプ　イベントが起きた時間
- rootBounds root領域の情報 デフォルトはbody要素)
- boundingCLientRect 監視要素の領域情報
- intersectionRect 交差領域の情報
- intersectinRatio 交差領域の割合(監視要素の全体の領域に対して何割交差しているか)
- isIntersecting 交差中かどうか


また、`IntersectionObserver`のコンストラクタには第２引数にオプションをつけられます。

- root 交差を検知するベースの要素を指定(デフォルトはbody要素)
- threshold 交差検知のタイミングを複数指定できる 交差割合の指定で0.0-1.0までの間
- rootMargn 交差する領域を広げることができる。

```js

window.addEventListener("DOMContentLoaded", () => {
  // インスタンス作成。コールバック関数は監視対象となる要素が視覚的に見えたり、消えたかどうかで実行される
  const observer = new IntersectionObserver((snapshot) => {
    console.log(snapshot);
  }, {
    threshold: [0.0, 0.5, 1.0],
    rootMargin: '10px'
  });

  // 監視対象の要素を取得
  const target = document.querySelector("#target");

  // 監視対象を設定
  observer.observe(target);
});
```

上記の例だと、`10px`交差領域を広げて、また交差割合が`0%,50%,100%`の時にイベントが発生するようになっています。

この発生するイベントとそこから取得できる情報を使って、アニメーション処理などを行うことができます。


## Renderingにおけるパフォーマンスチューニング

### スタイルのマッチング処理を減らす

レンダリングエンジンはCSSのセレクタとDOM要素を総当たりでマッチング処理を行います。
総当たりで行うためなるべく計算量の少ないマッチング処理が求められます。

１つ重要になるのは、マッチング処理は**CSSセレクタの右側から左側に向けて行われる**ということです。

例えば、下記のようなCSSセレクタがあった場合、ブラウザはこのようにマッチングを行います。


```css
body > div.logo {

}
```

1. class属性にlogoが含まれている
2. その要素がdivである
3. その親要素がbodyである

このように右側からマッチング処理を行なっていきます。セレクタをより細かく指定すればするほどマッチングの処理時間がかかるのがわかります。


そのためにパフォーマンスに影響が出やすいセレクタ指定はなるべく避けるべきです。

セレクタ指定は下記を意識して行うと良いでしょう。

- 余計な階層をつけずにシンプルなセレクタにする
- 子孫セレクタや間接セレクタを避ける
- 全称セレクタとの組み合わせを避ける

#### 余計な階層をつけずにシンプルなセレクタにする

前途のようにセレクタが詳細になればなるほど、マッチンング処理には時間がかかります。
セレクタをシンプルにできるのであればそのようにしましょう

```css
table > tbody > tr > td /* マッチング処理に時間がかかる */
td /* １回のマッチング処理で終われる */
```

シンプルに保つことで、アプリケーション全体のセレクタ詳細度も統一化されて、CSSの保守のしやすさにも繋がります。

#### 子孫セレクタや間接セレクタを避ける

子孫セレクタがあると、多くの祖先を辿ってマッチした要素がないかを探らなくてはなりません。

```css
#some-page .header {

}
```

上記の例だと、headerというclassを持った要素の祖先にsome-pageというidを持った祖先要素がないかをルートの要素まで行わなくてはなりません。
例えば、子セレクタにすれば、マッチング処理を行う量は限定的になります。

```css
#some-page > .header {

}
```

間接セレクタも同じようにマッチング処理を行う要素が多くなってしまう場合はなるべく使わない方が良いでしょう。

#### 全称セレクタとの組み合わせを避ける

全称セレクタを他のセレクタと組み合わせることでマッチングの処理は多くなってしまいます。

```css
.header * {

}
```

このようにすると、スタートが全ての要素になります。そこから全ての要素１つ１つに対して祖先要素に`class="header"`の要素がないかを調べます。DOM要素が多くなるにつれて計算量が格段に増えていきます。このような指定の仕方は避けた方が無難です。

### 使用していないCSSルールを削除する

CSSに書かれているルールセットはそのスタイルが実際に使われているいないに関係なくマッチング処理が行われます。そのため、使われていないルールセットがあると無駄にマッチング処理が行われています。

手動で使われていなルールセットを探しても良いですが、ツールを使って自動で削除しても良いでしょう。

[UNCSS](https://github.com/uncss/uncss)ではウェブサイトのURLを指定することでそのサイトで使用されていないルールセットを削除し新しいCSSファイルを生成してくれます。
webpackでは[purifycss-webpack](https://github.com/webpack-contrib/purifycss-webpack)というプラグインがあるのでこちらを使ってみるのも良いと思います。

### 非表示要素のレイアウトコストを減らす

CSSで要素を非表示にするには主に２パターンあると思います。

1. visibility:hidden や opacity: 0 を使う
2. display: noneを使う

1はtransitionアニメーションで表示非表示をする際に扱いやすいですが、他の要素で変更があった時にLayout処理が行われてしまいます。
一方2の場合は、Layout処理の対象から外れてるため、非表示要素に対してのレイアウト処理が行われなくなります。

非表示の要素を扱う際には可能ならばdisplay: noneを使うと良いでしょう。


### 画像のサイズを指定する

画像サイズを絶対値で指定できる場合は、あらかじめimgタグのwidth, height属性値を指定しましょう。ブラウザはサイズの指定がない場合は、画像が読み込まれる前に一旦仮の大きさでLayout計算やレンダリング処理が行われます。その後画像が読み込まれてサイズがわかるので再度レンダリング処理が行われます。

サイズが指定されていない画像が多いほど、その分レンダリングの処理が行われます。

画像サイズをあらかじめ指定しておけば、ブラウザは初めからそのサイズで計算を行うので、再計算を行いません。サイズ指定が可能なものはサイズ指定をしましょう。

## Paintingでのパフォーマンスチューニング

### Composite Layersのみを起こしてスタイルを変更する

Composite LayersはPaintingフェーズの最後の工程で、スタイル変更をここ工程のみで完結できればパフォーマンス的には良いでしょう。

Composite Layersが引き起こされる変更は下記になります。

- opacityの変更
- transformの変更

アニメーション処理などでは上記２つのプロパティを使うことでオーバーヘッドが少なくスタイルを変更できます。

### GPUを使う

Composite Layersは複数生成されたレイヤーを合成する工程です。レイヤーはzIndexでの上下関係のあるCSSやtransformで3D変形がある場合など、特定の条件のもと生成されます。

そのうちtransformで3D変形ある場合は、GPUで処理されるようになります。
GPUで処理することによりCPUで行われる処理と分散でき、高速に処理できるようになります。


よく使われるハック的な指定で `translateZ(0)`があります。

```css
.traget {
  transform: translateZ(0);
}
```
上記のように指定すると、`.traget`要素のComposite Layers工程はGUPで行われます。

Composite Layersの工程が頻繁に起こる場合は指定するとオーバーヘッドを軽減できると言われています。

例えば、position: fixedを使ってる要素は、描画位置を固定するためスクロールのたびにComposite Layersが起こります。

そのため、`translateZ(0)`を指定することで、処理をGUPに依頼することができます。

```css
.header {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100px;
  transform: translateZ(0);
}
```