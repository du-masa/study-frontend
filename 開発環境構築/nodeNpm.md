# なぜ環境構築が必要か

フロントエンド開発に起きる問題や面倒なことをできるだけ取り除きたい(開発効率を向上したい)

## 具体的には・・・
- CSSやJSの外部リソースのリクエスト数は抑えたいが、ファイル分割して開発したい
- レガシーブラウザ対応したい
- ファイル変更の監視したい
- JSライブラリの依存関係解決をしたい

などなど


# 開発環境の準備

## 兎にも角にもnode.js

前途した問題を解決するためには、node.js及びnpmを使うことが前提になります。

node.jsはサーバーサイドでかけるjavascript、
npmはnode.js製のモジュール群を管理します。

このnpm使って開発に必要なモジュールやライブラリをインストールしていきます。

### node.jsのインストール

インストール方法は主に2つ

- [公式サイト](https://nodejs.org/ja/)からインストーラーをダウンロード
- nodebrewやnvmを利用してインストール(windowはnodist)

推奨はnodebrewやnvmを使ったインストールです。
これらは、node.jsのバーション管理がコマンドライン上でできるためバージョンの切り替えが楽です。

#### nodebrewを使ったnode.jsのインストール
例としてnodebrewを利用したnode.jsのインストールを記載します。

##### 1. nodebrewのインストール

```bash
$ curl -L git.io/nodebrew | perl - setup
```

##### 2. PATHを通す

bash_profile等にnodebrewのパスを通すように設定する

```bash
$ cd
$ vi ~/.bash_profile
```

```vi
# パスを追加
export PATH=$HOME/.nodebrew/current/bin:$PATH
```

パスを通したら、bash_profileを更新して nodebrewコマンドが使えるようになればOKです。

```bash
$ source ~/.bash_profile
$ nodebrew help
```

##### 3. node.jsのインストール

node.jsをインストールします

```bash
#インストールできるnode.jsのバージョンを確認する
$ nodebrew ls-remote

・
・
・

v6.0.0    v6.1.0    v6.2.0    v6.2.1    v6.2.2    v6.3.0    v6.3.1    v6.4.0
v6.5.0    v6.6.0    v6.7.0    v6.8.0    v6.8.1    v6.9.0    v6.9.1    v6.9.2
v6.9.3    v6.9.4    v6.9.5    v6.10.0   v6.10.1   v6.10.2   v6.10.3   v6.11.0
v6.11.1   v6.11.2   v6.11.3   v6.11.4   v6.11.5   v6.12.0   v6.12.1   v6.12.2
v6.12.3

v7.0.0    v7.1.0    v7.2.0    v7.2.1    v7.3.0    v7.4.0    v7.5.0    v7.6.0
v7.7.0    v7.7.1    v7.7.2    v7.7.3    v7.7.4    v7.8.0    v7.9.0    v7.10.0
v7.10.1

v8.0.0    v8.1.0    v8.1.1    v8.1.2    v8.1.3    v8.1.4    v8.2.0    v8.2.1
v8.3.0    v8.4.0    v8.5.0    v8.6.0    v8.7.0    v8.8.0    v8.8.1    v8.9.0
v8.9.1    v8.9.2    v8.9.3    v8.9.4

v9.0.0    v9.1.0    v9.2.0    v9.2.1    v9.3.0    v9.4.0

・
・
・

# v8.9.4 をインストール
$ nodebrew install-binary v8.9.4

# インストールしたv8.9.4を使う
$ nodebrew use v8.9.4

# node.jsのバーション確認
$ node -v
v8.9.4

# npmも同時にインストールされる
$ npm -v
5.6.0
```
これでnode.jsを使えるようになりました。

nodebrewのインストールについてはこちらもご確認ください
[NodeBrewインストール編](https://qiita.com/ebisennet/items/15c3c9fdb04e66db6323)

### nvmやnodistのインストール

- nvm
[いまアツいJavaScript！ゼロから始めるNode.js入門〜5分で環境構築編〜](https://liginc.co.jp/web/programming/node-js/85318)


- nodist
[nodistでNode.jsをバージョン管理](https://qiita.com/satoyan419/items/56e0b5f35912b9374305)

## npmを使ったプロジェクト管理(をやってみる)

npmを使ってプロジェクトの初期化、必要なモジュールをインストールしてみましょう

### 1. プロジェクトディレクトリを用意
```bash
# 適当なディレクトリに移動
$ cd /path/to/workspace

# プロジェクトディレクトリ作成&移動
$ mkdir npm-project1 && cd npm-project1
```

### 2. プロジェクトの初期化
```bash
$ npm init -y

/path/to/workspace/npm-project/package.json

{
  "name": "npm-project",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC"
}
```

カレントディレクトリにpackage.jsonファイルが作成されます。
このjsonファイルにプロジェクトに必要なモジュールの情報やビルド時に使うコマンドなどを記載します。

### 3. モジュールをインストール

npmを使ってjqueryをインストールしてみます

```bash
$ npm i jquery
```

新たにnode_modulesディレクトリとpackage-lock.jsonファイルが作成されます。

- **node_modules** npmインストールしたモジュール類(依存するものも含まれる)が格納される
- **package-lock.json** 依存関係やバージョン管理を容易するためのファイル。パフォーマンス向上、実行環境に依存せず同一のインストールを保証する。npm5からの機能
[npm5から導入された package-lock.jsonについて](http://kakts-tec.hatenablog.com/entry/2017/06/05/020037)

また、package.jsonにdependenciesという項目が増えて、そのプロパティにjqueryが追加されてます。

```package.json
"dependencies": {
  "jquery": "^3.2.1"
}
```

このようにnpmでインストールしたモジュール類はpackage.jsonに記載されます。
そのためpackage.jsonを他の人に共有すれば、同じような開発環境を作ることが可能になります。

### 4. 別のモジュールをインストール

もう一つ、別のモジュールをインストールしてみましょう

```bash
$ npm i node-sass -D
```
node-sass(sassをcssにコンパイルできる)というモジュールをインストールしました。
再びpackage.jsonを見るとdevDependenciesという項目が増えて、そこにnode-sassプロパティが書かれています。

```package
  "devDependencies": {
    "node-sass": "^4.7.2"
  }
```

devDependenciesに書かれているモジュールもプロジェクトに必要なものとしてpackage.jsonで管理されます。

#### dependencies と devDependencies の違い

dependenciesは実際に開発されるサービスで使用するものを指定します。例としては、jqueryやangularなど、サービスが依存しているモジュール類です。

devDependenciesは開発時にのみ必要になるモジュールを指定します。例としては、node-sassやwebpackなどのコンパイラやビルドツールです。

devDependenciesとしてインストールする場合は、`npm i <モジュール名> -D` というように`-D`オプションを指定します。

ですが、一般的なweb開発においてはどちらでインストールしても問題ありません。

どんな時に違ってくるかというと、開発したサービスをnpmモジュールとして他の人に公開した時に変わってきます。
誰かが、その公開したモジュールをインストールした時、dependencies書かれたモジュールは依存関係があるとして一緒にインストールされます。devDependencies書かれたモジュールはインストールされません。

詳しくはこのあたりを
- [【いまさらですが】package.jsonのdependenciesとdevDependencies](https://qiita.com/chihiro/items/ca1529f9b3d016af53ec)
- [ちゃんと使い分けてる? dependenciesいろいろ。](https://qiita.com/cognitom/items/acc3ffcbca4c56cf2b95)

### 5. インストールしたモジュールを使って見る

#### htmlでjquery読み込み

htmlを作ってインストールしたjqueryを読み込んできましょう

```bash
$ touch index.html
```

```index.html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>npm-project</title>
    <link rel="stylesheet" href="./style.css">
</head>
<body>
    <script src="./node_modules/jquery/dist/jquery.min.js"></script>
    <script>
        document.addEventListener('DOMContentLoaded', function() {
            console.log($);
        });
    </script>
</body>
</html>

```
node_modulesにインストールされたjqueryをhtmlで読み込みます。
ブラウザのコンソールで$関数の内容が表示されれば問題ありません。

これだけだとCDNの方が楽な感じもしますが、依存するライブラリが増えてくるとnpmで管理する方が便利です。

#### node-sassでscssをコンパイルする

scssファイルを使って開発し、cssにコンパイルしてhtmlに読み込んでみましょう

```bash
$ touch style.scss
```


```style.scss
$list-color: #8cc63f;

ul {
	padding:0;

	li {
		color: $list-color;
		border-bottom: 1px solid $list-color;
	}
}
```

```index.html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>npm-project</title>
    <link rel="stylesheet" href="./style.css">
</head>
<body>
	<ul>
		<li>
			リスト2
		</li>
		<li>
			リスト2
		</li>
	</ul>
    <script src="./node_modules/jquery/dist/jquery.min.js"></script>
    <script>
        document.addEventListener('DOMContentLoaded', function() {
            console.log($);
        });
    </script>
</body>
</html>

```

```bash
# node-sassを使って style.scssからstyle.cssを作成
$ $(npm bin)/node-sass style.scss style.css
```
style.cssファイルが出来上がったと思います。このスタイルシートをindex.htmlで読み込むと背景が赤くなり、
ちゃんとコンパイルされていることがわかります。
前途でインストールしたnode-sassをしたことで、
sassコンパイルをCLIを使ってできるようになります。今回はそれを使用しました。

##### $(npm bin)について

node-sassのコマンドはパスがグローバルに通っていないので、本来ですとコマンドの位置を相対的に指定して、実行する必要があります。
コマンドは`./node_modules/.bin/`にあります。そのため先ほどのコンパイルコマンドは正式には次のようになります。

```bash
$ ./node_modules/.bin/node-sass style.scss style.css
```

ただ、やや冗長になります。
そこで `npm bin` が `./node_modules/.bin`とパス(正確には絶対パス)を吐き出すのでそれに置き換えてます。
(`$()`で囲んでいるのは、linuxコマンドで変数を参照するに必要なため)

##### ファイル変更を監視してみる

scssファイルを変更するたびにコマンドを叩いて、コンパイルしていた面倒です。
node-sassのCLIには様々なオプションがあります。その一つにファイルの監視をするオプションがあります。

```bash
$ $(npm bin)/node-sass style.scss style.css -w
```

これで、style.scssが監視状態になりました。scssのスタイルを変えるたびにコンパイルされてstyle.cssの内容も変わることがわかります。

#### npm-scriptsを使う

監視可能になったとはいえ、まだコマンドが冗長的です。
よりシンプルにするためにはnpm-scriptsを使います。

package.jsonのscirptsという項目を下記にように置き換えます。

```package.json
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "watch": "node-sass style.scss style.css -w"
  },
```

watchというプロパティを追加し、その値として先ほどのscssコンパイル用コマンドを記載してます。

これで、`npm run <コマンドを指定したプロパティ名>` で呼べるようになります。

```bash
$ npm run watch
```

npm-scirptsのメリットは下記です。

- グローバルにパスを通さなくても、グローバルなコマンドとして扱える。 `npm bin`を書かなくて済む。
- 将来node-sassを別のコンパイラを置き換えたとしても、実行コマンドを変えずに済む

基本的に開発で使うコマンドはnpm-scriptsで一元管理することをお勧めします。

参考: [npm で依存もタスクも一元化する](https://qiita.com/Jxck_/items/efaff21b977ddc782971)

# おすすめ開発環境

## やりたいこと
- cssのコーディング簡易化 & ファイル分割管理
- ベンダープレフィックス付与の自動化
- jsのモジュール管理(ファイル分割・依存関係解決)
- jsのES2015(以降)対応
- ファイル変更の監視
- ブラウザの自動リロード & 外部機器でのアクセス
- ソースマップ
- minify


## cssのコーディング簡易化 & ファイル分割管理

[scss](http://sass-lang.com/)を使うことで解決します。
scssはcssのメタ言語でcssを効率的に書くために開発されたものです。
例えば、変数やネスト(cssプロパティを簡易的に書く)といった仕組みがあります。

ファイルも分割して管理でき、最終的に１つのcssを生成してくれます。

ファイルを分割するメリットとしては、
- コードの見通しが良くなる
- 再利用しやすくなる
- 分担作業がしやくすなる
などあります。

scssの文法等についてはこちらに詳しく書いてあります。

[次世代のCSS記法、Sass入門 〜 実行環境構築から基本的な機能について 〜](http://xn--web-oi9du9bc8tgu2a.com/intro-sass/)

## ベンダープレフィックス付与の自動化

レガシーなブラウザでcss3を対応させるにはベンダープレフィックスをつける必要があります。
ただ手動でやろうとすると、ブラウザの数だけ対応しないといけないので骨が折れます。

そんな時は、[autoprefixer](https://github.com/postcss/autoprefixer)というモジュールを使うと自動でベンダープレフィックスを付与してくれます。
オプションで対応するブラウザのバージョンも指定できるので、切り捨てるブラウザの切り分けも自動でできます。

## jsのモジュール管理(ファイル分割・依存関係解決)


元来javascriptでは、複数のライブラリ・モジュールを読み込む場合はhtmlでそれぞれ読み込んであげる必要がありました。


```index.html
<script src="https://code.jquery.com/jquery-3.2.1.min.js"></script><!-- jquery -->
<script src="app.js"></script><!-- 独自で書いたjs -->
```

小規模のjsアプリケーションの場合は、すぐにできるのでオススメですが、
scriptタグで読み込むやり方は下記のような問題点があります。
- グローバルオブジェクトの衝突を気にかけないといけない
- ファイルの読み込む順番を気にかけないといけない
- 大規模なプロジェクトでは読み込むファイル数が膨大になり、管理することが困難になる
- HTTPリクエスト数も多くなる

そのため、js内で他のモジュールを読み込むやり方が登場しました。
[CommonJSやAMD](https://tsuchikazu.net/javascript-module/)などのモジュールシステムもありますが、jsの標準仕様である[ES modules](https://sbfl.net/blog/2017/07/26/es-modules-basics/)が現在の主流になっています。


さらに、[webpack](https://webpack.js.org/)というビルドツールを使用すると、モジュールを１つにまとめてくれて、その際に依存関係を自動的に解決してくれます。

webpackを利用するメリットはこちらにも記載されています。

[なぜwebpack（モジュールバンドラ）を利用するのか](https://qiita.com/soarflat/items/28bf799f7e0335b68186#%E3%81%AA%E3%81%9Cwebpack%E3%83%A2%E3%82%B8%E3%83%A5%E3%83%BC%E3%83%AB%E3%83%90%E3%83%B3%E3%83%89%E3%83%A9%E3%82%92%E5%88%A9%E7%94%A8%E3%81%99%E3%82%8B%E3%81%AE%E3%81%8B)

また、[browserify](http://browserify.org/)というモジュールでも複数のjsモジュールを１つにまとめることができます。


## jsのES2015(以降)への対応

ES2015の正式名称はECMAScript2015と言います。
ECMAScriptとは、javascriptの標準仕様の事で、このECMAScriptを基に書くブラウザが実装したものをjavascriptとして使っています。2015というのはバージョンを表しています。

ECMAScript2015はそれまでのECMAScript5に比べて多くの新機能を追加しました。以降`ES20**`という形でその年ごとに新しく策定された使用を標準化しています。

[ECMAScriptとJavaScriptの関係](https://app.codegrid.net/entry/ecmascript-1)

ただ、この新しい仕様をそのまま使おうとすると、まだ対応仕切れていないブラウザでエラーになってしまいます。
そのため、新しい仕様を使う場合にはトランスパイラと呼ばれるものを使用して、ブラウザで動かせるコードに変換します。

トランスパイラには[babel](https://babeljs.io/)が使われます。

また、前途に紹介したwebpackと一緒に使うことで、
モジュールを１つにまとめつつコンパイルをしてくれます。

ES2015で新しく追加された仕様については下記を参考にしてください。

[ES2015 (ES6)についてのまとめ](https://qiita.com/tuno-tky/items/74ca595a9232bcbcd727)

## ファイル変更の監視

前途したscssやbabelを使う場合、コンパイル(トランスパイル)が必要になります。
ただ、開発時に毎回何かを変更するたびにコンパイルするためのコマンド等を叩いていたら面倒です。

それを解決するためにファイルに変更があったら自動でコンパイルする(ファイル監視)という機能を使うと便利です。

webpackなどのビルドツールにはwatchという機能があり、それを使うことで対象のファイル変更を監視することできます。

## ブラウザの自動リロード & 外部機器でのアクセス

フロントエンド開発にはブラウザが必ず必要です。
開発をしている際には、何度もブラウザをリロードして実装内容を確認すると思います。
手動で行っているリロードを、ファイル変更されたら自動的されたら便利です。

また、PCブラウザでの確認だけでなくSPブラウザで確認したいという時があると思います。
いちいちネットワーク上のサーバーにアップロードするのも手間です。
アップロードせず、開発中のPCにアクセスして確認できるとようにします。

これらは、簡易的なローカルサーバーを立ち上げることで解決できます。

[webpack dev server](https://github.com/webpack/webpack-dev-server)や[browsersync](https://browsersync.io/)というローカルサーバーを立ち上げるモジュールを使うことで実現できます。

## ソースマップ

scssファイルなど、複数のファイルに分割することでコードの見通しは良くなりました。
しかしコンパイル後のscssファイルは、ブラウザのデベロパーツールから見たときは１枚のcssファイルにまとめられています。
修正しようと思ったらどのファイルをその記述が書かれているか、探さないと見つかりません。

ソースマップという機能を使うと、
デベロッパーにどのscssファイルにその記述がどこに書かれているか表示されるようになります。

**ソースマップなし**

![スクリーンショット 2018-01-16 19.04.00.png](https://qiita-image-store.s3.amazonaws.com/0/83765/4459d170-52b2-e4b2-d5bc-f4e6a10198da.png "スクリーンショット 2018-01-16 19.04.00.png")

**ソースマップあり**

![スクリーンショット 2018-01-16 19.03.08.png](https://qiita-image-store.s3.amazonaws.com/0/83765/7b50b957-93b5-2e82-bd96-9765765d5f73.png "スクリーンショット 2018-01-16 19.03.08.png")

## minify(ファイル圧縮)

cssやjsなどのファイルをに書かれている、
スペースや改行などファイルのサイズを大きくする部分を削って、圧縮するという機能があります。
これをminifyと言います。
本番にアップするファイルは、このminifyをすることで無駄なリソースをダウンロードしなくて済みます(パフォーマンス向上)