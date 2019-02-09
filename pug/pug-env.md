# pugの環境設定

webpackを使ったpugのコンパイル(HTMLファイル化)を紹介します。更新テスト

## リポジトリ

[https://github.com/du-masa/pug](https://github.com/du-masa/pug)

## モジュールインストール

下記のモジュールをインストールします。

```
$ npm i -D pug pug-loader html-webpack-plugin
```

## webpackに設定

`.pug`ファイルをpug-loaderを使ってコンパイルします。

```js
  module: {
    rules: [
      {
        test: /.pug$/,
        use: ['pug-loader']
      },
```

また、pug-loaderのみだとHTMLとして出力されないため、`html-webpack-plugin` を使って
どのpugファイルをどの言った名前のhtmlに出力するかを設定します。

`html-webpack-plugin`はwebpackのpluginsに設定します。

```js
  plugins: [
    new HtmlWebpackPlugin({
      template: './pug/index.pug'
    }),
    new HtmlWebpackPlugin({
      filename: 'list.html',
      template: './pug/list.pug'
    }),
    new HtmlWebpackPlugin({
      filename: 'detail.html',
      template: './pug/detail.pug'
    }),
    new HtmlWebpackPlugin({
      filename: './second/index.html',
      template: './pug/second/index.pug',
      minify: false
    }),
```

引数のオブジェクトを渡して詳細を設定します。
`template` には対象となるpugファイルのパス、`filename` には出力するhtmlのパスを指定します。
(`filename`を指定しなかった場合はindex.htmlになります)


## pugファイルの自動検出

`html-webpack-plugin`を使えばHTMLの出力はできるのですが、ページ数が増えた時にいちいち追加設定をするのが面倒です。
そのために自動で読み込めるようにスクリプトを追加してましょう

こちらの内容は[webpack.config.autoload.js](https://github.com/du-masa/pug/blob/master/webpack.config.autoload.js)に書いてあります。

13-22行目あたりのコードで設定しています。

```js
const from = 'pug';
const to = 'html';
const htmlPluginConfig = globule.find([`**/*.${from}`, `!**/_*.${from}`], {cwd: opts.srcDir}).map(filename => {
  const file = filename.replace(new RegExp(`.${from}$`, 'i'), `.${to}`).split('/')
  return new HtmlWebpackPlugin({
    filename: filename.replace(new RegExp(`.${from}$`, 'i'), `.${to}`).replace(/(\.\/)?pug/, '.'),
    template: `./${filename}`
  })
})
```

[globule](https://www.npmjs.com/package/globule)というnpmのモジュールを使って、該当のpugファイルを抽出しています。
戻り値は、該当のファイル名が入った配列なので、そのファイル名１つ１つに`html-webpack-plugin`の設定をしています。(設定一つ一つを配列に格納 => htmlPluginConfig)


あとはhtmlPluginConfigをwebpackのpluginsに設定することで自動的にpugファイルが増えてもHTMLを出力してくれるようになります。(70行目あたり)
```js
  plugins: [
    ...htmlPluginConfig,
```

## file-loaderで画像も取り扱う

file-loaderを使うことでpug内で読み込まれた画像も取り扱うことができます。

```js
  {
    test: /.(jpe?g|png|gif|svg)/,
    use: [
      {
        loader: 'file-loader',
        options: {
          name: '[name].[ext]',
          outputPath : 'image/',
          publicPath : 'image'
        }
      },
    ]
  }
```

pug内で画像を読み込むときは、`require`関数を使う必要があります。

```pug
  img(src=require("path/to/imagefile"))
```

https://github.com/pugjs/pug-loader#embedded-resources

## vue componentでpugを使う

vue componentのtemplateにHTMLではなくpugを使う場合は、
npm から pug本体をインストールするだけで使えるようになります。

```bash
$ npm i -D pug
```

pugをインストールした状態で、vueコンポーネントにpugを使う宣言をします。

```html
<template lang="pug">
</template>
```

参考: https://vue-loader-v14.vuejs.org/ja/configurations/pre-processors.html
