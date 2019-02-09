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

## Nuxt.jsで気をつけること

### SSRでのサーバー負荷

SSRは、サーバーに負荷がかかりやすい処理と言われています。トラフィックが多く見込まれる場合はサーバーでのキャッシュ対策などの対応が必要になります。

### 実行環境にNode.jsが必要

Nuxt.jsでSSRを行うには、サーバーにNode.jsの環境が必要になります。AWSやGCPなどのクラウドサーバーにはNode.jsの環境が整っていますが、レンタルサーバーなど安価なサーバーには備わってないことがあります。
実際にアプリケーションを動かす環境を事前に確認して使用する必要があります。(Nuxt.jsを使って静的にHTMLを生成する場合は、こちらに当てはまりません。)