# Nuxt.jsについて

## 今回の目的

Nuxt.jsとはどんなもので、何が出来るかを理解していただき、
今後使用するきっかけ作りになればいいかと思います。

## Nuxt.jsとは

Vue.jsアプリケーションを作成するフレームワークです。
アプリケーションを作るための最低限の機能やそれを実現するためのモジュール群を予め用意してくれるため、環境構築に手間をかけずに開発を始められます。

例えば、ルーティング機能をVue.jsで使用するには[Vue-Router](https://router.vuejs.org/ja/)を使用します。
通常なら、設定用のjsファイルを手動で用意するのですが、Nuxt.jsの場合は既に設定されており、
該当のディレクト(pageディレクトリ。詳しくは後述します)にファイルを作るだけで勝手にルーティングの設定が行われます。

## Nuxt.jsで出来ること

### サーバーサイドレンダリング(SSR)

個人的に一番の使用理由だと思います。
通常javascriptでAPIでデータを取得してVue.jsでレンダリングすると、クライアントサイドでのレンダリングになります。
その場合、問題になってくるのでSEOやSNSのOGP関連です。(クローラーやsns側がAPIのデータを取得出来ず、うまく表示されない)

Nuxt.jsでは[Vue Server Renderer](https://ssr.vuejs.org/ja/)の機能を使い、SSRを実現できるようにしています。
初期描画はサーバーサイドで行います。(PHPなどで作られる通常のwebサイトと同じ)
その後のサイト内遷移に関してはSPAと同じようにVue-Routerを使ってページを切り替えます(高速!!)

また、サーバーサイドとクライアントサイドの実装を同じコードで実現できるのも一つの特徴です。

### ルーティング機能

前途しましたが、Nuxt.jsではVue-Routerを使ったルーティング機能がデフォルトで用意されています。
Pageという特殊なディレクトリが用意されていて、その中に任意のvueファイルを作成するとそのページが自動で出来上がり、ルーティング設定も行われます。

### ビルド機能

Nuxt.jsでは基本的なビルド機能が備わっています。ES6/ES7のトランスパイルやミニファイなど、webpackの設定を手動で行わなくても出来るようになっています。(webpackはNuxt.jsのフレームワーク内部で使用されている)

また、`sass-loader`などのローダーは必要に応じてインストールするだけで自動で使えるようになり、さらにオプションの拡張もconfigファイル(nuxt.config.js)から行えるようになっていて、柔軟な構造になっています。

### 開発サーバー

開発時のローカルサーバーも組み込まれています。
ホットリローディングにも対応しているため、コードを変更したらすぐにブラウザに反映されます。

### 静的化

SSRでなく、予めHTMLを生成することも可能です。
そのため、動的なHTMLの生成が必要としない場合でも、静的サイトとして作ることができます。

### その他

その他にも便利な機能が備わっているので詳しくはこちらを確認してください。
[主な機能](https://ja.nuxtjs.org/guide#%E4%B8%BB%E3%81%AA%E6%A9%9F%E8%83%BD)


## Nuxt.jsの使い所

### Vue.jsを使った中・大規模開発

Vue.jsを使った中・大規模は開発を行う際は、おすすめです。前途したようにWebアプリケーションに必要な機能は予め用意されています。
また、フレームワークであるため、どのディレクトリにどのファイルを入れるかなどある程度ルールが決まっているため、それに沿って作ることで
ファイル構成の一貫性が保ちやすくなります。

### SEOが重視されるサイト

APIでデータを取得しVue.jsでレンダリングを行うサイトで、SEO対策も必要となるサイトはNuxt.jsのSSR機能を使って開発することをおすすめします。
SEOだけでなくOGPの内容も事前にサーバーサイドで用意すること出来るので、SNS側も正しい情報を取得できます。

逆にいうと、SEOやOGPの対応が必要ない場合(管理画面など)は無理に入れずに[vue-cli](https://cli.vuejs.org/)を使って開発しても良いと思います。(もしくはNuxt.jsのSPAモード)

## Nuxt.jsを使う上で気をつけること

### SSRでのサーバー負荷

SSRは、サーバーに負荷がかかりやすい処理と言われています。トラフィックが多く見込まれる場合はサーバーでのキャッシュ対策などの対応が必要になります。

### 実行環境にNode.jsが必要

Nuxt.jsでSSRを行うには、サーバーにNode.jsの環境が必要になります。AWSやGCPなどのクラウドサーバーにはNode.jsの環境が整っていますが、レンタルサーバーなど安価なサーバーには備わってないことがあります。
実際にアプリケーションを動かす環境を事前に確認して使用する必要があります。(Nuxt.jsを使って静的にHTMLを生成する場合は、こちらに当てはまりません。)

## インストール

今回は公式サイトに掲載されている`create-nuxt-app`を使ったインストールを行います。

[Nuxt.js クイックインストール](https://ja.nuxtjs.org/guide/installation#-code-create-nuxt-app-code-%E3%82%92%E4%BD%BF%E7%94%A8%E3%81%99%E3%82%8B)

```

$ npx create-nuxt-app <project-name>

```

`<project-name>`は任意の名前を指定します。

インストールが始まると対話式でいくつか聞かれます。

```bash

? Project name (project-name) #プロジェクト名
? Project description (My great Nuxt.js project) #プロジェクト詳細
? Use a custom server framework (Use arrow keys) #サーバーサイドのフレームワーク 今回は none
? Choose features to install # 必要なモジュールをインストールします 今回は何も選ばない
? Use a custom UI framework # UI フレームワークを選びます 今回は none
? Use a custom test framework # unitテストのツールを選びます 今回は none
? Choose rendering mode # レンダリングモードを選びます 今回は Universal
? Author name # ユーザー名
? Choose a package manager # パッケージマネージャーを選択します 今回は npm

```

上記を選択していくとインストールが開始されます。
`npm install`も同時に行ってくれます。

下記コマンドで、アプリを起動できます。

```bash

$ cd <project-name>

$ npm run dev

```

[http://localhost:3000](http://localhost:3000)でアプリケーションにアクセスできます。

※ スクラッチでインストール、環境設定を行うこともできます。
[スクラッチでインストール](https://ja.nuxtjs.org/guide/installation#%E3%82%B9%E3%82%AF%E3%83%A9%E3%83%83%E3%83%81%E3%81%8B%E3%82%89%E5%A7%8B%E3%82%81%E3%82%8B)


## ファイル・ディレクトリ構造

create-nuxt-appを使ってインストールするとデフォルトでファイルやディレクトリが用意されています。
それぞれ役割が分かれているので、幾つかのファイル・ディレクトリについて説明します。

### nuxt.config.js

Nuxt.jsの設定ファイルになります。Nuxt.jsが予め用意している設定を変更・拡張したい場合にこちらのファイルを変更します。
configファイルの中身はオブジェクトが書かれたjsファイルで、各種プロパティに対して設定を行っていきます。

[nuxt.config.jsプロパティ一覧](https://ja.nuxtjs.org/guide/configuration)

例えば、webpackの設定を拡張したい場合には、`build`プロパティ設定を追加します。  
同じように、独自の環境変数を追加したい場合は、`env`プロパティに変数を追加します。


このように、アプリケーションに関数設定は、`nuxt.config.js`ファイルに集約します。

### pagesディレクトリ

各ページとなる`*.vue`ファイルを入れるディレクトリです。  
(HTMLでいうと、index.htmlとかlist.htmlとかdetail.htmlなど、実際のページとなるファイルです)

このディレクトリに入れたファイルは自動的にVue-Routerによってルーティング設定されます。
そのため、開発者が手動でルーティング設定を行う必要がありません。

静的なルーティングだけでなく、動的なルーティング(記事ページで`post/[id]`みたいにidが動的に変わるページなど)の生成も行えます。

### componentsディレクトリ

Vue.jsのコンポーネントファイルを入れます。  
`pages`ディレクトリに入れたファイルから呼ばれるVueコンポーネント全てをこのディレクトリに集約します。

### layoutsディレクトリ

アプリケーション全体のレイアウトを構成する`*.vue`ファイルを入れます。  

レイアウトファイルに、アプリケーション全体で使われるヘッターやフッターなどを書いておくことでアプリケーション全体で統一したデザインを作れます。

また、レイアウトファイルは複数作れるので、ページごとにレイアウトの変更も可能です。

### assets ディレクトリ

scss、js、画像などビルド前のファイルを格納します。 
グローバルなcssやjsはこちらのディレクトリに入れると良いです。

### その他

[Nuxt.jsディレクトリ構造](https://ja.nuxtjs.org/guide/directory-structure)


## ページを追加してみよう

ページを追加するには、`pages`ディレクトリに`*.vue`ファイルを追加します。

`pages/index.vue`ファイルをコピーして、名前を`detail.vue`としましょう。

そして、`detail.vue`のh1タグのテキストを変換します。

```html
<h1 class="title">
    project-name
</h1>

<!-- 下に変更↓↓↓↓ -->

<h1 class="title">
    detail
</h1>

```

出来たら、[http://localhost:3000/detail](http://localhost:3000/detail)にアクセスします。

## `pages/*.vue`ファイルで使えるメソッド

`pages`ディレクトリ内の`*.vue`ファイルでは、通常のVueメソッド以外にも使えるメソッドあります。
SSRを行う上で必要になってくるメソッドが幾つかあるので紹介します。

### asyncDataメソッド

サーバーサイドでAPIからデータを取得して、コンポーネントの`data`としてセットしたい場合に使います。
`asyncData`メソッド内でデータを取得することができるので、SEOやOGPの設定に有効です。

また、サーバーサイドでの実行は初回描画のみで、Vue-Routerを使ってページを遷移した場合はクライアントサイドで実行されます。  
(サーバー・クライアントサイド、両方同じコードで実行できる)

#### ログを出してみよう

`pages/detail.vue`ファイルの`asyncData`メソッドを使ってログを出してみましょう。


```js

export default {
  asyncData() {
    console.log('called asyncData')
  },
}
```

`asyncData`メソッド内でconsole.logでログをだす単純なプログラムを用意します。

一度、devサーバーを停止し、再度起動します。

[http://localhost:3000/detail](http://localhost:3000/detail)にアクセスすると、
ターミナルに`called asyncData`と表示されます。

このことから、ブラウザ側で実行されたのでなく、サーバー側で実行されたのがわかります。  
  
次に、クライアントサイドでログを出してみましょう。  
Vue-Routerを使って[http://localhost:3000/detail](http://localhost:3000/detail)にアクセスすることで、
ブラウザでログを確認できます。

まず、`pages/index.vue`ファイルに`detail`ページへのリンクを設置します。

```html
<!-- 22行目に追加 -->
<nuxt-link to="detail" class="button--grey">detail</nuxt-link>
```

`nuxt-link`タグは、Vue-Routerを使ったリンクの書き方です。`to`属性にはページの名前を指定します。(今回はdeital)

[http://localhost:3000/](http://localhost:3000/)にアクセスします。そして、先ほど追加したボタンをクリックします。
そうするとページが遷移し、ブラウザのconsoleに`called asyncData`が表示されます。


これにより、`asyncData`メソッドの処理は初期アクセスはサーバーサイド、それ以降のページ内遷移はクライアントサイドで実行されていることがわかると思います。

#### データを設定する

`asyncData`メソッドでは、APIなどで取得したデータをVueコンポーネントのデータに設定することができます。

一旦APIは使わずに、静的なデータを設定してみましょう。

コンポーネントにデータをセットする場合は、`asyncData`メソッドの戻り値にオブジェクトを指定します。

オブジェクトのkeyが各データ名、valueがデータ値になります。

実際に設定してみましょう。


```html
<template>
  <div>
    {{newData}}
  </div>
</template>

<script>
export default {
  asyncData() {
    return {
      newData: 'set data from asyncData'
    }
  },
}
<script>
```

今回は、`newData`というデータを用意するため、戻り値のオブジェクトにkeyに`newData`を用意して、値を`set data from asyncData`にしています。 
そして、テンプレート側に`{{newData}}`と記述することで、ブラウザ上で`set data from asyncData`が表示されます。

これは次のコードに置き換えることができます。

```html
<template>
  <div>
    {{newData}}
  </div>
</template>

<script>
export default {
  data() {
    return {
      newData: 'set data from asyncData'
    }
  }
}
<script>
```

違うのは、`asyncData`の場合はサーバーサイドであらかじめデータをセット出来るということです。
これはSEOやOGPの設定に有効に働きます。

#### APIからデータを取得してみる

今度はAPI経由でJSONを取得し、コンポーネントのデータに反映しましょう。

API通信するHTTPクライアントは`axios`を使います。(`create-nuxt-app`の設定で、既にインストール済みです)

APIは以前使用した`json-server`を使いましょう。

リポジトリ: [https://github.com/du-masa/json-server](https://github.com/du-masa/json-server)  
json-server使い方: [https://study-frontend.netlify.com/jsonServer](https://study-frontend.netlify.com/jsonServer/)


リポジトリが用意できたら、APIサーバーを立てましょう。

```bash
$ npm i 
$ npm run api-server
```

立ち上がったら、今回は`http://localhost:3030/posts`というエンドポイントを使ってJSONを取得しましょう。

`pages/detail.vue`の中身を下記のにします。

```html
<template>
  <div>
    {{posts}}
  </div>
</template>

<script>
import axios from 'axios' 
export default {
  asyncData() {
    return axios.get('http://localhost:3030/posts')
    .then((res) => {
      return {
        posts: res.data
      }
    })
  }
}
<script>
```

APIで取得したデータを、`posts`というデータ名で用意してコンポーネントで使用できるようにしてます。
ブラウザでアクセスして、`http://localhost:3030/posts`のJSONの中身が表示されればOKです。

### headメソッド

`head`メソッドは、htmlの`head`タグの記述を設定できます。 
通常のSPAだと`head`タグの中身をjsで切り替えただけでは、クローラーなどにうまく認識されません。 
`head`メソッド使ってheadタグの中身を設定することで、事前にサーバーサイドでレンダリングしてくれます。


例えば、`title`タグの内容を設定するには下記のようにします。

```html
<script>
import axios from 'axios' 
export default {
  head() {
    return {
      title: '詳細ページです',
    }
  }
}
<script>
```

`head`メソッドの戻り値をオブジェクトにして、keyに`title`、valueに実際に表示するタイトルのテキストを指定します。

`meta`タグは複数存在するので、配列を指定して、`meta`タグごとの属性情報を指定したオブジェクトを格納します。

```html
<script>
import axios from 'axios' 
export default {
  head() {
    return {
      title: '詳細ページです',
      meta: [
        { name: 'description', content: 'ディスクリプションディスクリプションディスクリプション' },
      ]
    }
  }
}
<script>
```

上記は、`meta`タグの`description`を指定してます。

#### APIから取得したデータをheadタグの中身に埋め込む

`asyncData`メソッドを使ってAPIから取得したデータは`head`メソッド内で使用できます。

`http://localhost:3030/posts/1`にアクセスすると、一つの記事情報が取得できます。 
これを使って記事のタイトルを`title`タグの中身に設定してみましょう。

```html
<script>
import axios from 'axios' 
export default {
  asyncData() {
    return axios.get('http://localhost:3030/posts/1')
    .then((res) => {
      return {
        post: res.data
      }
    })
  },
  head() {
    return {
      title: this.post,
      meta: [
        { name: 'description', content: 'ディスクリプションディスクリプションディスクリプション' },
      ]
    }
  }
}
<script>
```

#### その他

`head`メソッドは[vue-head](https://github.com/ktquez/vue-head)というモジュールを内部で利用しています。  
`title`や`meta`タグ以外にも`head`タグ内で使用するタグの指定方法が載っていますので、参考にしてみてください。


## 参考文献
- [Nuxt.js公式サイト](https://ja.nuxtjs.org/)
- [Vue-Router](https://router.vuejs.org/ja/)
- [Vue Server Renderer](https://ssr.vuejs.org/ja/)
- [vue-cli](https://cli.vuejs.org/)
- [vue-head](https://github.com/ktquez/vue-head)
- [json-server](https://github.com/typicode/json-server)