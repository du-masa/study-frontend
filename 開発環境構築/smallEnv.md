# 小さく始める開発環境

とにかく手っ取り早く最小機能で始めたい場合おすすめです。

## browser-sync

開発時にローカルサーバーを立てて、
なおかつファイルを更新したら自動でブラウザリロードされると便利です。
また、同一ネットワークであれば外部端末でアクセスできるので、スマホデバックでも活用できます。

[公式サイト](https://browsersync.io)

### インストール

```bash
$ npm i browser-sync -D
```

### サーバー起動

```bash
$ npx browser-sync start --server
```

デフォルトではルートディレクトリのindex.htmlが開かれます。

### 外部機器アクセス

前途のコマンドで起動するとターミナルで下記のように表示されます。


![スクリーンショット 2018-02-10 14.10.55.png](https://qiita-image-store.s3.amazonaws.com/0/83765/5258b352-4069-8e51-55e3-82bb8368d7e1.png "スクリーンショット 2018-02-10 14.10.55.png")

Externalというところに表示されているのは、自分のPCのIPアドレスとbrower-syncで起動したポート番号のURLです。
サーバー起動したPCと同じネットワークに接続した端末で、Externalに表示されているURLでアクセスすると同じページにアクセスできます。

### 設定ファイル

細かいサーバーの設定はconfigファイルに予め書いておくと便利です。
configファイルはコマンドで初期化できます。


```bash
$ npx browser-sync init
```

`bs-config.js`ファイルが出来上がります。
こちらに細かく設定できるようになってます。

下記コマンドでconfigファイル使ってサーバーを起動できます。

```bash
$ npx browser-sync start --server --config bs-config.js
```

### 設定内容
`bs-config.js`ファイルで設定できるオプションを
いくつか紹介します。


#### port

ポート番号を指定できます。
デフォルトは3000です。

```bs-config.js
// localhost:8000でアクセス可能になる
port: 8000,
```

#### files

監視の対象となるファイル名(ディレクトリ名)を指定します。
ここに書かれたファイル群が変更されたら自動的にリロードされます。

```bs-config.js
// './public/main.css'が変更されたらリロードされる
files: './public/main.css',

// 配列で複数指定も可能
files: ['./public/main.css', './public/main.js']

// glob指定も可能 ./public以下のすべてのhtmlファイルを監視
files: './public/**/*.html'
```

#### server

rootディレクトリやindexとなるhtmlなどを指定できます。

```bg-config.js

server: {
	baseDir: 'public',
	index: 'index.html',
	serveStaticOptions: {
          extensions: ["html"]
        }
}

```

serveStaticOptionsのextensionsに拡張子を入れると、URLで拡張子を省略した形でアクセスできます。

※ serverの設定をしても、コマンド側で `--server` オプションをつけてしまうと、デフォルトの設定が優先されてしまいます。
つまり、 `rootディレクトリのindex.html` に一番最初にアクセスします。

configファイルでserverの設定をした場合のコマンドは下記にします。

```
$ npx browser-sync start --config bs-config.js"
```

#### proxy

PHPなどサーバーサイドの言語をローカル環境で動かしている場合、browser-syncでは動かせません。
mampや仮想環境(virtualBoxなど)でローカル環境を用意している場合、そのIPをproxyに設定することで、browser-syncの機能を使えるようになります。

```bg-config.js

// localhost:8888をbrowser-syncで表示できるようにする
proxy: {
	target: 'localhost:8888',
}

```

proxyを設定した場合は、serverの値をfalseにする必要があります。

※ proxyを設定しても、コマンド側で`--server` オプションをつけてしまうと下記のようなエラーになってしまいます。

```
Invalid config. You cannot specify both server & proxy options.
```

serverを設定と同様、proxyを設定した場合のコマンドは下記になります。

```
$ npx browser-sync start --config bs-config.js"
```

### コマンドをnpm-scriptsで管理する

browser-syncのコマンドを毎回打つのは面倒なのでpackage.jsonにコマンドを登録しておきましょう。

```package.json

scripts: {
  "server": "browser-sync start --server --config bs-config.js"
}

```

## autoprefixer

ベンダープレフィックスを自動で付与してくれます。

[autoprefixer](https://github.com/postcss/autoprefixer)

autoprefixerは[postcss](http://postcss.org/)というcssを変換するためのツールが必要になります。

### インストール

```bash
$ npm i postcss-cli autoprefixer -D
```


### ビルド

では、実際にベンダープレフィックスを自動付与してみましょう

```bash
$ touch style.css
```

```css
div {
  transform: scale(0.5);
}
```

```bash
$ npx postcss style.css --use autoprefixer -o style.build.css
```

style.cssに対して、autoprefixerの処理を行いstyle.build.cssを出力しています。
style.build.cssの中身は下記のようにベンダープレフィックスが付与されていると思います。

```style.build.css
div {
  -webkit-transform: scale(0.5);
      transform: scale(0.5);
}
```

### 対象ブラウザの指定

どのブラウザを対象とするかをオプションで指定することができます。

package.jsonの`browserslist`という項目に記載することでブラウザを指定できます。

```package.json

"browserslist": [
	"last 2 versions"
]
```

上記では、各ブラウザごと最後の2バージョンを対象にベンダープレフィックスを付与するように設定しています。

この指定は[browserslist](https://github.com/ai/browserslist)というものに基づいて指定しています。
このリストに基づけば、より詳細に設定することができます。

### 監視とnpm-srciptsでのコマンド管理

最後にファイル監視とpackage.jsonへのコマンド登録をしましょう。

```package.json
scripts: {
  "server": "browser-sync start --config bs-config.js",
  "postcss": "postcss style.css --use autoprefixer -o style.build.css -w"
}
```

`-w`でファイル変更を監視できます。

[demo: browserSync + autoPrefixer](https://github.com/zap-mueda/frontend-env/tree/master/browserSync-autoPrefixer)

## node-sass

scssを使う場合は、`node-sass`を使います。

### インストール

```bash
$ npm i -D node-sass
```

### ビルド

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

```bash
$ npx node-sass style.scss style.css
```

これでscssファイルをcssにコンパイルできます。

### autoprefixerの処理と連動させる

前節で設定したautoprefixer処理と連動させましょう。
scssからコンパイルされたcssをpostcssで監視すれば、scssを変更したタイミングで
自動的にベンダープレフィックスが付与されます。

package.jsonにコマンドをまとめて書きます。

```package.json
scripts: {
  "server": "browser-sync start --config bs-config.js",
  "postcss": "postcss style.css --use autoprefixer -o style.build.css -w",
  "scss": "node-sass style.scss style.css --source-map-embed -w",
  "styles": "npm run scss & npm run postcss"
}
```

scssコンパイル用のnpm-scripsを用意しています。
さらにstylesというscriptsで、2つのコマンドをまとめています。

```bash
$ npm run styles
```

これで、style.scssを変更すれば、scssのコンパイル・ベンダープレフィックスの自動付与を行ってくれます。

### プロダクト用を用意する

cssのminifyやソースマップ削除など、プロダクト用のビルド処理も用意してみましょう。

各コマンドのオプションを変更することで実現できます。

```package.json
"scripts": {
    "server": "browser-sync start --config bs-config.js",
    "postcss": "postcss style.css --use autoprefixer -o style.min.css -w",
    "scss": "node-sass style.scss style.css --source-map-embed -w",
    "styles": "npm run scss & npm run postcss",
    "postcss:prod": "postcss style.css --use autoprefixer -o style.min.css --no-map",
    "scss:prod": "node-sass style.scss style.css --output-style compressed",
    "styles:prod": "npm run scss:prod & npm run postcss:prod"
  },
```

`postcss:prod` `scss:prod` `styles:prod` という3つのscriptsを追加してます。

行っているのは下記になります。
- watch処理の削除
- scssのoutput-styleをcompressedに変更
- ソースマップの出力を無効

このように、開発時は `npm run styles` サーバーアップ時は `npm run styles:prod` と言った感じで
コマンドを分けて管理します。

## browser-syncの監視対象を設定して、ビルドコマンドをまとめる

browser-syncのconfigファイルで、監視対象を設定します。

```bs-config.js

"files": ["style.min.css", "index.html"],
```

開発時に使用するコマンドもまとめましょう

```package.json
"scripts": {
    "server": "browser-sync start --config bs-config.js",
    "postcss": "postcss style.css --use autoprefixer -o style.min.css -w",
    "scss": "node-sass style.scss style.css --source-map-embed -w",
    "styles": "npm run scss & npm run postcss",
    "postcss:prod": "postcss style.css --use autoprefixer -o style.min.css --no-map",
    "scss:prod": "node-sass style.scss style.css --output-style compressed",
    "styles:prod": "npm run scss:prod & npm run postcss:prod",
    "start": "npm run server & npm run styles"
  },
```

`start`というscriptsを追加しました。

```bash
$ npm start
```

上記コマンドでローカルサーバー起動とscssコンパイル処理ができるようになると思います。
* `npm run start`でも同じ処理ができますが、`start` はもともと用意された名前なので `run` を省略できます。

[demo: browserSync + autoPrefixer + node-sass](https://github.com/zap-mueda/frontend-env/tree/master/browserSync-sass-autoPrefixer)

## その他のモジュール紹介

### browserify

ファイル分割した(モジュール分割)jsファイルを1ファイルにまとめてくれる

[公式サイト](http://browserify.org/)

[demo: browserSync + browserify](https://github.com/zap-mueda/frontend-env/tree/master/browserSync-browserify)

### babel

ES2015以降の仕様をブラウザに対応した形にトランスパイルしてくれる
[公式サイト](https://babeljs.io/)
[babel-cli](https://babeljs.io/docs/usage/cli/)

[demo: browserSync + browserify + babel](https://github.com/zap-mueda/frontend-env/tree/master/browserSync-browserify-babel)

## まとめ

いくつかのモジュールを紹介しながら、簡単な開発環境を作ってきました。
このように、各モジュールのコマンドを自由に組み合わせて作りたい環境を用意することができます。
設定も、コマンドの引数やオプションを組み合わせることでほぼできてしまいました。
難しい設定ファイル等いらないのがこのシンプルな環境いいところだと思います。