# pug

## pugとは

Node.js製のテンプレートエンジン(旧名:jade)
https://pugjs.org/api/getting-started.html

pugのメリットは下記です。
- タグで囲んだり、属性値を省略して書くことが可能で、記述をシンプルになる
- ファイルを分割して、includeすることで、可読性や再利用性が高まる
- 変数、if文、ループ文を使うことができる

## タグ

タグはタグ名だけでHTML使う山カッコ(<>)は必要ありません。
また、閉じタグも必要ありません

```pug
ul
```

```html
<ul></ul>
```

## 入れ子

HTMLの入れ子構造は改行+インデントまたは、コロンで表現します。

```pug
// 改行+インデント
ul
  li
    a

// コロン
ul: li: a
```

```html
<ul>
  <li><a></a></li>
</ul>
```

ブロックレベル要素は改行+インデント、インライン要素はコロンと使い分けると読みやすいしれません。

## 属性

id,classは特別な表記で表現できます。
idは`#(シャープ)`、classは`.(ドット)`で表現します。(jqueryのセレクタと同じですね)

```pug
ul#list
  li.listItem
    a
```

```html
<ul id="list">
  <li class="listItem"><a></a></li>
</ul>
```

そのほかの属性値は`丸括弧()`で表現します。

```pug
ul#list
  li.listItem
    a(href="https://google.com" target="_blank")
```

```html
<ul id="list">
  <li class="listItem"><a href="https://google.com" target="_blank"></a></li>
</ul>
```

## テキストの表示
タグ内にテキストを書くには、タグの後にスペースを入れて書きます。

```pug
p テキストテキスト
```

```html
<p>テキストテキスト</p>
```


## 改行・コメント


改行は`br`タグと`(|パイプ)`を組み合わせます

```pug
p
  |トップページのコンテンツ
  br
  |トップページのコンテンツ
```

```html
<p>トップページのコンテンツ<br>トップページのコンテンツ</p>
```

コメントは`//(スラッシュ２つ)`で表現します。バックスラッシュの後に`-(ハイフン)`を追加するとHTMLに出力されません。

```pug
// HTMLで書き出されるコメント

//- HTMLで書き出されないコメント
```

```html
<!-- HTMLで書き出されるコメント -->
```

## 変数

任意の変数を使うことができます。

### 変数宣言

宣言は `-(ハイフン)`の後に`var宣言`でできます。

```pug
- var title = 'タイトル'
```

### 変数の展開

変数の展開は`#{変数}`で行います。

```pug
h1 #{title}
```

```html
<h1>
  タイトル
</h1>
```

## if文

変数などの条件に応じて表示する内容を出し分けられます。

```pug

if flag
  p flagがtrueだった時の表示
else
  p falseがfalseだった時の表示
```

```html
<!-- flagがtrueの時 -->
<p>flagがtrueだった時の表示</p>

<!-- flagがfalseの時 -->
<p>flagがfalseだった時の表示</p>
```

後述するmixin機能などで、共通のコードで変数の値を分けて出し分ける際などに便利です。


## 繰り返し(for, each)

同じ要素が続く場合など、繰り返しの構文を使うと便利です。

### for文

javascriptのfor文を使って要素を必要数繰り返します。

```pug
ul
- for(var i = 0; i < 3; i++)
  li.listItem
    a(href="https://google.com" target="_blank") item#{i + 1}
```

```html
<ul id="list">
  <li class="listItem">
    <a href="https://google.com" target="_blank">
      item1
    </a>
  </li>
  <li class="listItem">
    <a href="https://google.com" target="_blank">
      item2
    </a>
  </li>
  <li class="listItem">
    <a href="https://google.com" target="_blank">
      item3
    </a>
  </li>
</ul>
```

### each

eachを使うことで配列使って繰り返し処理をすることができます。

```pug
ul
  each item in ['item1', 'item2', 'item3']
    li.listItem
      a(href="https://google.com" target="_blank") #{item}
```

```html
<ul id="list">
  <li class="listItem">
    <a href="https://google.com" target="_blank">
      item1
    </a>
  </li>
  <li class="listItem">
    <a href="https://google.com" target="_blank">
      item2
    </a>
  </li>
  <li class="listItem">
    <a href="https://google.com" target="_blank">
      item3
    </a>
  </li>
</ul>
```

## include

UIを部品化(ファイル分割)して、表示したい箇所で読み込むことで再利用性を高められます。

例えば、header部分を別ファイルに記述して、必要なpugテンプレートで`include`で読み込むようにします。
＊includeで読み込むファイルは、HTMLが生成されないよう`_(アンダーバー)`をつけた名前にしています。

```pug
//- common/_header.pug
header
  h1 logo
```

```pug
//- index.pug
body
  include ./common/_header.pug
  main.index
```

```html
<body>
  <header>
    <h1>logo</h1>
  </header>
  <main class="index">
  </main>
</body
```

```pug
//- list.pug
body
  include ./common/_header.pug
  main.list
```

```html
<body>
  <header>
    <h1>logo</h1>
  </header>
  <main class="list">
  </main>
</body
```

## extends

ベースとなるHTMLの構造(headタグやヘッター、フッターなど)を用意し、それを各ページで継承することで、
共通のレイアウトを利用することができます。

まずはベースとなるレイアウト用のファイルを用意します。

```pug
//- _layout.pug
html
  head
    title My Site - #{title}
    block scripts
      script(src='/jquery.js')
  body
    block content
    block foot
      #footer
        p some footer content
```

`block XXX`と書かれた場所にはレイアウトの継承先で自由にタグを追加して書くことができます。

継承先を見てみましょう。

```pug
//- index.pug
doctype html
extends _layout.pug
- var title = 'index'

block content
  div#index
    section
      p index content

block foot
  div#index-footer
    ul
      li
```

```html
<!DOCTYPE html>
<html>
  <head>
    <title>My Site - index</title>
    <script src="/jquery.js"></script>
  </head>
  <body>
    <div id="index">
      <section>
        <p>index content</p>
      </section>
    </div>
    <div id="index-footer">
      <ul><li></li></ul>
    </div>
  </body>
</html>
```

呼び出しがで`block XXX`と宣言し、そこから改行とインデントでタグを書いていくことによって
`_lauout.pug`で指定した箇所にHTMLタグが生成されます。

また、特に指定しなかったblockは、`_lauout.pug`で指定された内容が吐き出されます。
(上記の例だと`block script`)

`append`を使うことで、上書きでなく追加することも可能

```pug
block append scripts
  script(src="/vue.js")
```

```html
<script src="/jquery.js"></script>
<script src="/vue.js"></script>
```

## mixin

includeでUIを分割して共通化することができました。
しかし、若干内容が異なる場合があります(テキストが違う、スタイルが少し違う)。
その場合は、mixinを使います。
関数のように引数を取れるのでテキストなどを動的にしたい場合に便利です。

```pug
//- mixin/_list.pug
mixin list(items)
  ul
    each item in items
      li #{item}
```

mixinは `mixin 名前` で宣言します。名前の後に任意で仮引数を設定できます。
その引数に応じて内容を動的に変更することができます。

```pug
//- index.pug

include mixin/_list.pug
+list(['item1', 'item2', 'item3'])
+list(['item4', 'item5', 'item6'])
```

呼び出す側は `+mixin名(引数)` で呼び出します。
＊mixinを別ファイルとして設定している場合、そのファイルincludeで読み込む必要があります。

```html
<ul>
  <li>item1</li>
  <li>item2</li>
  <li>item3</li>
</ul>
<ul>
  <li>item1</li>
  <li>item2</li>
  <li>item3</li>
</ul>
```

#課題

下記2つのHTMLをpugを使って作ってください。


```html
<!-- index.html -->
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8">
  <title>これはトップページです</title>
  <meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1, minimum-scale=1, user-scalable=no">
  <meta name="description" content="トップページのディスクリプションです">
  <link ref="stylesheet" href="style.css">
</head>
<body>
  <header id="header">
    <h1>pugの練習サイト</h1>
    <nav class="header__nav">
      <ul class="header__menu">
        <li><a href="/">トップ</a></li>
        <li><a href="/">一覧</a></li>
        <li><a href="/">詳細</a></li>
        <li><a href="/">問い合わせ</a></li>
      </ul>
    </nav>
  </header>
  <main id="main" class="index">
    <section class="indexSection">
      <h2>新着</h2>
      <ul class="list">
        <li class="list__item">
          <a href="/list/1">新着記事1</a>
        </li>
        <li class="list__item">
          <a href="/list/2">新着記事2</a>
        </li>
        <li class="list__item">
          <a href="/list/3">新着記事3</a>
        </li>
        <li class="list__item">
          <a href="/list/4">新着記事4</a>
        </li>
      </ul>
    </section>
    <section class="indexSection">
      <h2>ランキング</h2>
      <ul class="list">
        <li class="list__item">
          <a href="/list/4">ランキング記事1</a>
        </li>
        <li class="list__item">
          <a href="/list/10">ランキング記事2</a>
        </li>
        <li class="list__item">
          <a href="/list/11">ランキング記事3</a>
        </li>
        <li class="list__item">
          <a href="/list/44">ランキング記事4</a>
        </li>
      </ul>
    </section>
  </main>
  <footer id="footer">
    <p>&copy;2019 〇〇, Inc.</p>
  </footer>
</body>
</html>
```

```html
<!-- list.html -->
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8">
  <title>これは記事一覧です</title>
  <meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1, minimum-scale=1, user-scalable=no">
  <meta name="description" content="記事一覧のディスクリプションです">
  <link ref="stylesheet" href="style.css">
</head>
<body>
  <header id="header">
    <h1>pugの練習サイト</h1>
    <nav class="header__nav">
      <ul class="header__menu">
        <li><a href="/">トップ</a></li>
        <li><a href="/">一覧</a></li>
        <li><a href="/">詳細</a></li>
        <li><a href="/">問い合わせ</a></li>
      </ul>
    </nav>
  </header>
  <main id="main" class="index">
    <h2>〇〇記事一覧</h2>
    <ul class="list">
      <li class="list__item">
        <a href="/list/1">〇〇記事1</a>
      </li>
      <li class="list__item">
        <a href="/list/2">〇〇記事2</a>
      </li>
      <li class="list__item">
        <a href="/list/3">〇〇記事3</a>
      </li>
      <li class="list__item">
        <a href="/list/4">〇〇記事4</a>
      </li>
    </ul>
  </main>
  <footer id="footer">
    <p>&copy;2019 〇〇, Inc.</p>
  </footer>
</body>
</html>
```
