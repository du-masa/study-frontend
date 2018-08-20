
# webpack4

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

## インストール
webpackを使うにはまず、本体とコマンド実行用のCLIをインストールする必要があります。

```bash
$ npm i webpack webpack-cli -D
```

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
`webpack.config.js`に下記のように書きましょう。

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

- **entry:** ビルド対象となるファイル。ここに書かれたファイルのことをエントリーポイントと呼びます。
- **output:** 出力先のディレクトリとファイル名を指定。

- **output.path:** 出力先のディレクトリを指定します。ここは、絶対パスでないといけません。
- **output.filename:** 出力後のファイル名を指定します。`[name]`は、`entry`に指定したビルド対象となるファイル名が動的に入ります。今回の例ですと、`[name]`には`main`が入り、`main.bundle.js`というファイルが出来上がります。


`./assets/js/main.js`ファイルに適当にコードを書いてみます。

```main.js
console.log('success webpack bundle!');
```

この状態でビルドしてみましょう。

`package.json`にコマンドを指定します。

```package.json
"scripts": {
  "build": "webpack --mode development"
},
```

```bash
$ npm run build
```

`./public/js/main.bundle.js`というファイルが出来上がります。

※ webpackコマンドについている`--mode`オプションはwebpack4からの新機能です。
開発用と本番用のビルド内容をオプションに渡す引数によって自動的分けてくれます。
今回は、`development`としたので開発用にビルドしてくれます。


次に、jqueryをmain.js内に読み込んでビルドしてみましょう。

```bash
$ npm i jquery
```


```main.js
import $ from 'jquery';
console.log($);
```

出来上がった`main.bundle.js`を見てみると、jqueryがバンドルされていることがわかります。

[demo: webpack get started](https://github.com/zap-mueda/frontend-env/tree/master/wp4-init)

### loaderを使う
loaderと呼ばれるモジュールを使うことでbabelやscssを使ってバンドルすることができます。

#### babelをコンパイル

babelを使ってバンドルをする場合は、まずbabel本体とloaderとインストールします。
[babel-loaderのgithub](https://github.com/babel/babel-loader#install)でもインストールが必要となるモジュールを紹介しています。

```bash
$ npm i babel-core babel-preset-env babel-loader -D
```

インストールしたら`webpack.config.js`に下記の設定を行います。

```webpack.config.js

module.exports = {
    entry: './assets/js/main.js',
    output: {
        path: path.resolve(__dirname, 'public'),
        filename: 'js/[name].bundle.js'
    },
    module: {
        rules: [
            {
                test:/.js$/,
                exclude: '/node_modules/',
                use: {
                    loader: 'babel-loader',
                    options: {
                      presets: ['babel-preset-env']
                    }
                }
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

`public/`に`index.html`を作成して、`main.bundle.js`を読み込んでみましょう。

```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
  <script src="./js/main.bundle.js"></script>
</body>
</html>
```

#### scssをコンパイル

scssにもローダーがあります。それによりscssファイルをバンドルすることができます。

まずは、必要なモジュールをインストールします。
scss用のローダー以外に、scssコンパイルに必要なnode-sassやcssを扱うために必要なローダーも合わせてインストールします。

```bash
$ npm i sass-loader css-loader style-loader node-sass -D
```


```webpack.config.js

// scssファイルをバンドルしてをする

module.exports = {
    entry: {
    	main: ['./assets/js/main.js', './assets/scss/main.scss']
    },
    output: {
        path: path.resolve(__dirname, 'public'),
        filename: 'js/[name].bundle.js'
    },
    module: {
        rules: [
            {
                test:/.js$/,
                exclude: '/node_modules/',
                use: {
                    loader: 'babel-loader',
                    options: {
                      presets: ['babel-preset-env']
                    }
                }
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

[demo: webpack loader](https://github.com/zap-mueda/frontend-env/tree/master/wp4-loader)

### オプションを付け加えていく

その他の開発環境でやりたいことの設定を紹介していきます。

#### ベンダープレフィックス自動化

sass-loeaderとcss-loaderの間にpostcss-loaderを追加して、そのプラグインとしてautoprefixerモジュールを使います。

```bash
$ npm i postcss-loader autoprefixer -D
```

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

```bash
$ npm i extract-text-webpack-plugin@4.0.0-beta.0 -D
```

※ `extract-text-webpack-plugin`の最新版は、まだwebpack4に対応してません。インストール時に`@4.0.0-beta.0`をつけることで、webpack4に対応したbeta版を落とします。

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

さらに、新たにpluginsというプロパティを作り、その配列にExtractTextPluginのインスタンスを渡しています。引数は出力されるパス(`output.path`からの相対パス)とファイル名を指定します。

ビルドコマンドを実行すると`./public/css/main.css`が出来上がります。

`public/index.html`から`main.css`を読み込みましょう。

```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <link rel="stylesheet" href="./css/main.css">
</head>
<body>
    
  <script src="./js/main.bundle.js"></script>
</body>
</html>
```

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
・
・
・
  plugins: [
    new ExtractTextPlugin('./css/[name].css'),
    new UglifyJsPlugin() /* pluginsプロパティに追加 */
  ]
```

引数にオプションも渡せます。例えば、コメントは残すか残さないか。といった感じです。

[demo: webpack options](https://github.com/zap-mueda/frontend-env/tree/master/wp4-options)

また、cssを圧縮するには、css-loaderのオプションに指定します。

```webpack.config.js

{
  loader: 'css-loader',
  options: {
    sourceMap: true,
    minimize: true
  }
},
```

### 開発サーバー

開発サーバー用のサーバーは[webpack dev server](https://github.com/webpack/webpack-dev-server)を使用します。


webpack dev serverできることは、
- ファイルの監視
- auto reloead ([Hot Module Replacement](https://webpack.js.org/concepts/hot-module-replacement/)という部分リロードも可)
- 外部端末でのアクセス(スマホ・window検証などで使える)

```bash
$ npm i webpack-dev-server -D
```

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
  "build": "webpack --mode development",
  "start": "webpack-dev-server --inline --mode development"
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

[demo: webpack dev server](https://github.com/zap-mueda/frontend-env/tree/master/wp4-devserver)

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
  "build": "webpack --mode development",
  "build:prod": "webpack --mode production",
  "start": "webpack-dev-server --inline --mode development"
},
```

`build:prod`コマンドを追加して`--mode`オプションを`--mode production`に変更します。
`webpack.config.js`内で`mode`変数が使えるのでそれを判定材料に、処理を分けます。

`webpack.config.js`内で`mode`変数を参照するには、少し書き方を変えます。

```webpack.config.js

// before
module.exports = {
  // your settings...
};

// after
module.exports = (env, argv) => {
　console.log(argv.mode); // production もしくは development
　const PRODUCTION = argv.mode === 'production';
  return {
    // your settings...
  };
};

```

`module.exports`に代入する値を、オブジェクトから、オブジェクトを返す関数に変えます。第2引数の`argv`から`mode`を参照できます。

`argv.mode === 'production'`かどうかを比較し、`PRODUCTION`変数に代入します。
この`PRODUCTION`変数を使って処理を出し分けます。

```webpack.config.js
const path = require('path');
const webpack = require('webpack');
const ExtractTextPlugin = require('extract-text-webpack-plugin');
const UglifyJsPlugin = require('uglifyjs-webpack-plugin');

module.exports = (env, argv) => {
  const PRODUCTION = argv.mode === 'production';
  return {
    entry: {
      main: ['./assets/js/main.js', './assets/scss/main.scss']
    },
    output: {
        path: path.resolve(__dirname, 'public'),
        filename: 'js/[name].bundle.js'
    },
    devtool: !PRODUCTION ? "inline-source-map" : false,
    module: {
        rules: [
            {
                test:/.js$/,
                exclude: '/node_modules/',
                use: {
                    loader: 'babel-loader',
                    options: {
                      presets: ['babel-preset-env']
                    }
                }
            },
            { test: /\.scss$/,
              use: ExtractTextPlugin.extract({
                use: [
                  {
                    loader: 'css-loader',
                    options: {
                      sourceMap: !PRODUCTION,
                      minimize: PRODUCTION
                    }
                  },
                  {
                    loader: 'postcss-loader',
                    options: {
                      plugins: () => [
                        require('autoprefixer')({ browsers: ['last 2 versions'] }),
                      ],
                      sourceMap: !PRODUCTION
                      },
                  },
                  {
                    loader: 'sass-loader',
                    options: {
                      sourceMap: !PRODUCTION
                    }
                  },
                ]
              }),          
            }
        ]
    },
    plugins: [
      new ExtractTextPlugin('./css/[name].css'),
      ...(
        PRODUCTION ? [new UglifyJsPlugin()]: []
      )
    ],
    devServer: {
      contentBase: path.resolve(__dirname, 'public'),
      host: '0.0.0.0',
      disableHostCheck: true,
      port: 8080,
      publicPath: '/',
      watchContentBase: true
    },
  }
};
```


[demo: webpack deploy](https://github.com/zap-mueda/frontend-env/tree/master/wp4-deploy)


### 画像やフォントの扱い

css内で画像やフォントファイルなどを読み込む場合は、新たなローダーが必要になります。

#### file-loader

画像などのファイルを指定したディレクトリに吐き出します。

まずは、file-loaderをインストールします。

```bash
$ npm i file-loader -D
```

`assets/images`ディレクトリを作成し、適当な画像を格納しましょう。

```
root/
　├assets/ 
　│  └ js/ 
　│  └ scss/
　│  └ images/ ← imagesディレクトリを作成
```
　

`assets/scss/main.scss`でその画像を読み込みます。

```main.scss
body {
  background: black; }

div {
  display: flex;
  background-image: url('../images/00013594_72B.jpg');
}
```

※画像名は各々格納した画像名にしてください。

このまま、ビルドするとエラーになります。
そこでfile-loaderの設定をします。

```webpack.config.js
 module: {
        rules: [
        ・
        ・
        ・
         {
           test: /.(jpe?g|png|gif|svg)/,
           use: {
             loader: 'file-loader',
             options: {
               name: '[name].[ext]',
               outputPath : 'images/',
               publicPath : '../images'
             }
           }
         }

```

上記設定してビルドすることで、`public/images`ディレクトリに画像が格納され、cssで読み込まれた画像に対しても対応することができます。

設定内容を説明すると、

- **options.name**: 出力するファイル名を指定します。`[name]`には元となるファイル名、`[ext]`には元となるファイルの拡張子が入ります。
- **options.outputPath**: ファイルを出力するパスを指定します。`output.path`からの相対パスです。
- **options.publicPath**: ビルドで出力されたcssなどに指定するパス


webフォントなどのフォントファイルをcssで読み込む場合も同じように書けます。

```webpack.config.js
 module: {
        rules: [
        ・
        ・
        ・
         {
           test: /.(jpe?g|png|gif|svg)/,
           use: {
             loader: 'file-loader',
             options: {
               name: '[name].[ext]',
               outputPath : 'images/',
               publicPath : '../images'
             }
           }
         },
         {
            test: /\.(woff|woff2|eot|ttf|svg|otf)$/,
            use: [
              {
                loader: 'file-loader',
                options: {
                  name: '[name].[ext]',
                  outputPath: 'fonts/',
                  publicPath : '../fonts'  
                }
              }
            ]
          },

```

#### 画像圧縮

imageminで画像圧縮も行えます。
webpackで使うには`image-webpack-loader`を使います。

```bash
$ npm i image-webpack-loader -D
```

`webpack.config.js`で画像ファイルの処理をしている箇所に`image-webpack-loader`の設定を追加します。

```webpack.config.js

 module: {
        rules: [
        ・
        ・
        ・
         {
           test: /.(jpe?g|png|gif|svg)/,
           use: [
             {
               loader: 'file-loader',
               options: {
                 name: '[name].[ext]',
                 outputPath : 'images/',
                 publicPath : '../images'
               }
             },
             {
             	loader: 'image-webpack-loader',
             	options: {
                mozjpeg: {
                  progressive: true,
                  quality: 65
                },
                pngquant: {
                  quality: '65-90',
                  speed: 4
                },
                gifsicle: {
                  interlaced: false,
                },
               }
             },
           ]
         },
```

[demo: webpack file](https://github.com/zap-mueda/frontend-env/tree/master/wp4-file)

#### url-loader

`file-loader`では、cssで読み込まれている画像等を指定したディレクトリに出力しました。
それとは別に`url-loader`を使うことで[base64にエンコードし、css内にバンドルしてくれます。

[base64ってなんぞ？？理解のために実装してみた](https://qiita.com/PlanetMeron/items/2905e2d0aa7fe46a36d4)

url-loaderをインストールします。

```bash
$ npm i url-loader -D
```

`webpack.config.js`で画像の設定をしたfile-loaderの箇所を次のように置き換えましょう。

```webpack.config.js
 module: {
        rules: [
        ・
        ・
        ・
         {
            test: /.(jpe?g|png|gif|svg)/,
            use: [
              {
                loader: 'url-loader',
                options: {
                  limit: 1,
                  name: '[name].[ext]',
                  outputPath: 'images/',
                  publicPath : '../images'
                }
              },
              {
                loader: 'image-webpack-loader',
                options: {
                  mozjpeg: {
                    progressive: true,
                    quality: 65
                  },
                  pngquant: {
                    quality: '65-90',
                    speed: 4
                  },
                  gifsicle: {
                    interlaced: false,
                  },
                }
              },
            ]
          },

```

ビルドすると、`asstes/images/`ディレクトリに画像が出力されず、cssでのbase64が読み込まれる形になります。

`file-loader`にはなかったオプションがあります。


- **options.limit**: base64に変換するファイルサイズの上限を指定します。単位はバイトです。ここで数値したファイルサイズ以上の場合は、`file-loader`と同じように`outputPath`に指定したディレクトリに外部ファイルとして出力されます。

[demo: webpack url](https://github.com/zap-mueda/frontend-env/tree/master/wp4-url)


#### ビルド対象にならない画像について

scssや読み込まれている画像は`file-loader`や`url-loader`の対象になります。
しかし、htmlなどwebpackでビルド対象になっていないファイル内で読み込まれている画像の扱いは別対応が必要です。


対応例は幾つかあります。

① 初めから`public/images`ディレクトリで管理する。

前途したディレクトリ構造では、`assets/images`に画像を入れていました。
cssで読み込んでいる画像は自動で、`public/images`に出力されますが、
htmlで読み込んでいる画像は出力されません。

そのため、htmlで読み込む画像は、あらかじめ`public/images`に格納します。

```
root/
　├assets/ 
　│  └ js/ 
　│  └ scss/
　│  └ images/ ← scssやjsで読み込む画像
　├public/
　　 └ images/ ← htmlで読み込む画像
```

ただし、この方法だと、複数のディレクトリで画像を管理することになります。

② 別処理を用意するして`public/images`に出力する

htmlで読み込んでいる画像は、gulpなどの別処理を用意して、`assets/images`から`public/images`に出力するようにします。

こうすることによって①の問題点だった画像ディレクトリの２元管理を解消できます。

この問題点とすれば、画像処理が２つあることです。

③ webpackでscss内の外部ファイル処理を行わない

画像の処理を②で用意した処理だけにして、webpack側ではファイル処理をしないようにします。

`css-loader`のoptionsに指定します。

```webpack.config.js
 module: {
        rules: [
        ・
        ・
        ・
        use: [
          {
            loader: 'css-loader',
            options: {
              sourceMap: !PRODUCTION,
              minimize: PRODUCTION,
              url: false
            }
          },
```

`options.url`をfalseにします。

※こうすることで、fontファイルも処理されなくなるので、別途`assets`ディレクトリから`public`ディレクトリにコピーする処理が必要になります。

[demo: webpack file html](https://github.com/zap-mueda/frontend-env/tree/master/wp4-file-html)
