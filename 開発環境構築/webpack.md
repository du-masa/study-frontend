# webpack

webpackは今ではフロントエンドの開発環境の軸となるビルドシステムです。
webpackを通して、scssやbabelのコンパイル、ベンダープレフィックス付与など、各種やりたいことを実現できます。

## webpackによる環境設定

webpackはモジュールハンドラと呼ばれ、各種jsのモジュール(ライブラリ)の依存関係を解析して１つまたは複数のバンドルファイルを出力します。

## webpackでできること

### モジュールの同期・非同期読み込み、依存関係解決
前途したように、webpackではES modulesで書かれたモジュール読み込みの依存関係を解決し一つのファイルにまとめます。
また、モジュールは同期読み込みだけでなく、非同期読み込みもできです。つまりネットワーク経由でのモジュール読み込みも可能とします。

### コード分割
webpackを使えば、複数のjsファイルを１つにまとめることができます。
とはいえ、大規模なアプリケーションの場合、１つにまとめると該当のページで不要なコードを読み込むんでしまうことになります。
その場合、エントリーポイントとなる起点となるjsファイルを分けることで、必要なコードのみ使用するということが可能になります。

### jsだけを対象にしていない
js以外にもcssや画像なども設定によって対象とすることができます。
例えばscssのコンパイルなども行えます

## webpackの設定

webpackはCLIでも各種コンパイルを行えますが、
やりたいことが複数あるので、設定ファイルにまとめて書きます。
設定はwebpack.config.jsというファイルに書きます。

```bash
$ touch webpack.config.js
```

### とりあえずコンパイルしてみる

まず、
ビルド対象のファイルとその出力先を指定しただけの、簡単なコンパイルを行います。

```webpack.config.js
const path = require('path'); // ファイルパスの操作をするnode.jsのモジュール
const webpack = require('webpack'); // webpack本体

module.exports = {
    entry: './assets/js/main.js',
    output: {
        path: path.resolve(__dirname, 'public'),
        filename: 'js/[name].bundle.js'
    }
}
```

- **entry:** ビルド対象となるファイル。ここに書かれたファイルのことをエントリーポイントと呼びます
- **output:** 出力先のディレクトリとファイル名を指定

`./assets/js/main.js`ファイルに適当にコードを書いてみます。

```main.js
console.log('success webpack bundle!');
```

この状態でビルドしてみましょう

```bash
$ npm run build
```

`./public/js/main.bundle.js`というファイルが出来上がります。

試しにjqueryをmain.js内に読み込んでビルドしてみましょう。

```main.js
import $ from 'jquery';
console.log($);
```

出来上がったmain.bundle.jsを見てみると、jqueryがバンドルされていることがわかります。

[demo: webpack get started](https://github.com/zap-mueda/frontend-env/tree/master/wp-init)

### loaderを使う
loaderと呼ばれるモジュールを使うことでbabelやscssを使ってバンドルすることができます

babelを使ってバンドルをする場合は下記のように設定します。

```webpack.config.js

module.exports = {
    entry: './assets/js/main.js',
    output: {
        path: path.resolve(__dirname, 'public/js'),
        filename: '[name].bundle.js'
    },
    module: {
        rules: [
            {
                test:/.js$/,
                exclude: '/node_modules/',
                use: [
                    'babel-loader'
                ]
            }
        ]
    }
}
```

module.rulesプロパティの配列内にコンパイルの対象となるファイルごとにオブジェクトを設定していきます。

- **test**: 対象となるファイル(正規表現) 
- **exclude**: 除外するフォルダ
- **use**: 使用するローダーやそのローダーのオプション

babelでJSをバンドルする場合は、babel-loaderモジュールを使用します。

scssにもローダーがあります。それによりscssファイルをバンドルすることができます。

```webpack.config.js
// scssファイルをバンドルしてをする

module.exports = {
    entry: {
    	main: ['./assets/js/main.js', './assets/scss/main.scss']
    },
    output: {
        path: path.resolve(__dirname, 'public/js'),
        filename: '[name].bundle.js'
    },
    module: {
        rules: [
            {
                test:/.js$/,
                exclude: '/node_modules/',
                use: [
                    'babel-loader'
                ]
            },
            { test: /\.scss$/,
                use: [
                　 { loader: 'style-loader' },
                    { loader: 'css-loader' },
                    { loader: 'sass-loader' }
                ]
            }
        ]
    }
}
```

```bash
$ mkdir ./assets/scss
$ touch ./assets/scss/main.scss
```

```main.scss
body {
	background: black
}
```

module.rules配列に新たなオブジェクトを追加しました。scssファイルを対象にして、3つのloaderを使用してます。

- **sass-loader**: scssをバンドルできるようにします。(≒scssコンパイル)
- **css-loader**: cssをバンドルできるようにします。これによりjs内にcssを組み込まれた形でなる
- **style-loader**: jsにバンドルされたcssを動的にstyleタグとして表示します。

また、entryプロパティをオブジェクトにして、
その下にmainプロパティに変更しています。
そして配列にしてmain.jsとmain.scssをエントリーポイントとしています。
このようにすることでscssをバンドルの対象となります。(main.js側でscssを読み込んでも同じ動きになります)

[demo: webpack loader](https://github.com/zap-mueda/frontend-env/tree/master/wp-loader)

### オプションを付け加えていく

その他の開発環境でやりたいことの設定を紹介していきます。

#### ベンダープレフィックス自動化

sass-loeaderとcss-loaderの間にpostcss-loaderを追加して、そのプラグインとしてautoprefixerモジュールを使います。

```webpack.config.js
{
  test: /\.scss$/,
  use: [
      { loader: 'style-loader' },
      { loader: 'css-loader' },
      {
         loader: 'postcss-loader',
         options: {
           plugins: () => [
             require('autoprefixer')({ browsers: ['last 2 versions'] }),
           ],
          },
       },
       { loader: 'sass-loader' },
   ]
},

```

[autoprefixer](https://github.com/postcss/autoprefixer)のオプションで対象となるブラウザの指定もできます

#### CSS外部ファイル化

cssをstyleタグでなく、外部ファイルとして出力したい場合はもあります。
その場合は、[extract-text-webpack-plugin](https://github.com/webpack-contrib/extract-text-webpack-plugin)というwebpkackのプラグインを使用します。

```webpack.config.js
const path = require('path');
const webpack = require('webpack');
const ExtractTextPlugin = require('extract-text-webpack-plugin');

・
・
・

{
  test: /\.scss$/,
  use: ExtractTextPlugin.extract({
    use: [
      { loader: 'css-loader' },
      {
         loader: 'postcss-loader',
         options: {
           plugins: () => [
             require('autoprefixer')({ browsers: ['last 2 versions'] }),
           ],
          },
       },
       { loader: 'sass-loader' },
     ]
  }),
}

・
・
・
plugins: [
  new ExtractTextPlugin('./css/[name].css')
]
```

まずextract-text-webpack-pluginを読み込み、loaderのところをExtractTextPlugin.extractメソッドで囲ってます。(style-loaderは不要なので消してます)

さらに、新たにpluginsというプロパティを作り、その配列にExtractTextPluginのインスタンスを渡しています。引数は出力されるパスとファイル名を指定します。

ビルドコマンドを実行すると`./assets/css/main.css`が出来上がります。

#### ソースマップ
開発中はソースマップがあると便利です。
ソースマップを出力するもwebpackのオプションでできます。

```webpack.config.js
  /* 適当な位置に追加 */
  devtool: 'inline-source-map',
```

ビルドして、ブラウザのデベロッパーツールで見るとソースマップが出力されているのがわかります。
scssのソースマップを出力するにはloaderのオプションで指定する必要があります。

```webpack.config.js
{
  test: /\.scss$/,
  use: ExtractTextPlugin.extract({
    use: [
      {
        loader: 'css-loader',
        options: {
          sourceMap: true
        }
      },
      {
         loader: 'postcss-loader',
         options: {
           plugins: () => [
             require('autoprefixer')({ browsers: ['last 2 versions'] }),
           ],
           sourceMap: true
          },
       },
       {
         loader: 'sass-loader',
         options: {
          sourceMap: true
         }
       },
     ]
  }),
}
```

#### ファイル圧縮

ビルドファイルの圧縮はwebpackのプラグインで行えます。
[UglifyJsPlugin](https://github.com/webpack-contrib/uglifyjs-webpack-plugin)というプラグインを使用します。
こちらをインストールして、configファイルで指定します。

```bash
$ npm i uglifyjs-webpack-plugin -D
```

```webpack.config.js
const UglifyJsPlugin = require('uglifyjs-webpack-plugin');

  plugins: [
    new ExtractTextPlugin('./css/[name].css'),
    new UglifyJsPlugin() /* pluginsプロパティに追加 */
  ]
```

引数にオプションも渡せます。例えば、コメントは残すか残さないか。といった感じです。

[demo: webpack options](https://github.com/zap-mueda/frontend-env/tree/master/wp-options)

### 開発サーバー

開発サーバー用のサーバーは[webpack dev server](https://github.com/webpack/webpack-dev-server)を使用します。

webpack dev serverできることは、
- ファイルの監視
- auto reloead ([Hot Module Replacement](https://webpack.js.org/concepts/hot-module-replacement/)という部分リロードも可)
- 外部端末でのアクセス(スマホ・window検証などで使える)

webpack dev serverの設定もwebpack.config.jsにしていきます。

```webpack.config.js
/* 適当な箇所に追加 */
devServer: {
  contentBase: path.resolve(__dirname, 'public'),
  host: '0.0.0.0',
  disableHostCheck: true,
  port: 8000,
  publicPath: '/',
  watchContentBase: true
},
```

- **contentBase**: サーバーのrootとなるディレクトリ ここにindex.htmlを設置するとブラウザで開かれる
- **host**: アクセスするhost 何も書かないとlocalhostでアクセスできるが、外部端末からアクセス場合は0.0.0.0にする
- **disableHostCheck**: 外部端末からアクセスすためにはtrueにする。
- **port**: ポート番号
- **publicPath**: バンドルしたjsやcssの起点となるパス
- **watchContentBase**: contentBaseのファイルの変更も監視する。これによりバンドルされていないindex.htmlを監視対象になる

webpack dev serverを使用する際は、`webpack-dev-server`コマンドを使います。
package.jsonにコマンドを追加して、実行するとサーバーが立ち上がります。

```package.json
"scripts": {
  "test": "echo \"Error: no test specified\" && exit 1",
  "build": "webpack",
  "start": "webpack-dev-server --inline"
},
```

```bash
$ npm start
```

[localhost:8000](http://localhost:8000)でアクセスできます。

スマホ等外部端末でアクセスするには、
サーバーを立ち上げたPCを同一ネットワークに接続し、そのPCの[IPアドレス:8000]にアクセスします。

PCのIPが192.168.8.128だったら、192.168.8.128:8000

##### webpack dev serverはビルドファイルを出力しない

webpack dev serverはビルドファイル出力しません。サーバーを立ち上げでもpublicディレクトにはjsやcssは生成されません。ビルドしたファイルはメモリ上に保存されるらしいです。
余計な中間生成物が出力されずに済みます。

[demo: webpack dev server](https://github.com/zap-mueda/frontend-env/tree/master/wp-devserver)

### 開発用とプロダクト用にビルド処理を分ける

開発用には必要なビルド処理だけどプロダクト用には入らない、ということがあると思います。
その逆もしかりです。

例としては、

**開発用だけ**
- ソースマップ

**プロダクト用だけ**
- minifiy

余計な処理は、ビルド時間を増やし開発に支障が出る場合があります。
ここでは、開発用とプロダクト用でコマンドを分けて、webpackでの処理を分ける形をとります。

最初にコマンドを設定します。

```package.json
"scripts": {
  "test": "echo \"Error: no test specified\" && exit 1",
  "build": "webpack",
  "build:prod": "NODE_ENV=production npm run build",
  "start": "webpack-dev-server --inline"
},
```

`build:prod`コマンドを追加してnode.jsの環境変数に`NODE_ENV=production`を追加します。
この変数を判定材料として処理を出し分けます。

```webpack.config.js
const PRODUCTION = process.env.NODE_ENV === 'production';
```

コマンドで指定した`NODE_ENV`変数はjs側で`process.env.NODE_ENV`という名前で受け取れます。
その値が`production `ならプロダクト用、そうでなければ開発用となります。

このPRODUCTION定数を使って必要な処理を出し分けます。


[demo: webpack deploy](https://github.com/zap-mueda/frontend-env/tree/master/wp-deploy)