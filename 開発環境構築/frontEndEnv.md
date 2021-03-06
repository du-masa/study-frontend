# どういった開発環境を使うか

様々な機能がそろった環境を使って効率的に開発をできるのは魅力的です。
ただ、下記の点に注意が必要です。。

- 様々な機能を入れるには、そのためのモジュールやプラグインを追加する必要があり、設定に時間がかかる。
- モジュール・プラグインの組み合わせによってはエラーが発生したりして、その解消に時間を割かれることも・・・(バージョンの不一致など)。
- 処理が多いため、ビルド時間が増える場合もある。


このように、効率化しよう逆に開発効率を下げてしまうこともあります。

規模やスピード感といったプロジェクトに合わせた開発環境を使用するのが良いでしょう。

小規模用と大規模用に分けた開発環境を普段から用意しておくと便利です。

今回は、いくつかの開発環境を紹介します。

# npxのインストール

npmでローカルにインストールしたコマンドは、`$(npm bin)`(もしくは`./node_modules/.bin`)をつけないと使用できませんでした。
package.jsonのscriptsに書くことで解決しますが、環境作りの段階ではコマンドを色々と試したりするのでターミナルで直接打ち込みたいものです。

npxは`$(npm bin)`を置き換えてくれるコマンドです。

便利なためグローバルインストールしておきましょう。

```bash
$ npm i npx -g
```

使用するには下記のようにします。

```bash
$ npx [commond] [arguments] [options]
```

# 小さく始める開発環境

とにかく手っ取り早く最小機能で始めたい場合おすすめです。

[小さく始める開発環境](/smallEnv)

# 色々な処理をまとめた環境作り

開発環境で色々とやりたいと思ったら、それを実現するためのnpmモジュールをそれぞれ用意する必要があります。
前途したような各モジュールのコマンドの組み合わせて作ることも可能ですが、
量が増えていくと見通しが悪くなり、修正等のメンテナンス性も低下します。

そこで、タスクランナーやビルドシステムといった、
あらかじめconfigファイルやjsのスクリプトで各処理を書き、まとめてコマンド１つで実行できるようにします。

これらのツールはいくつかありますが、よく使われるのは[webpack](https://webpack.js.org/)と[gulp](https://gulpjs.com/)です。

その中でもwebpackは、今一番主流のツールとなっているのでこちらを使った開発環境について説明していきます。

## webpack

[webpack](/webpack)

## gulp

webpackが流行る前はgulpというタスクランナーがよく使われていました。
(もちろん今でも使われています。)

ここでは、gulpのメリット・デメリットを挙げます。
webpackと比較しながら、状況に応じて使い分けられれば良いかと思います。

### メリット
- 設定ファイルが(jsファイル)書きやすい・見やすい(.pipe連結)
- タスクという単位で処理が分けられるので、管理がしやすい
- 非同期処理/同期処理ができる

### デメリット
- gulpプラグインのサポートが製作者に委ねられる。(アップデート等で動かなくなることも)
- ドキュメントがバラバラ => プラグインによって設定内容が異なったりする(エラーメッセージの表示が自動なものもあれば、自分で書かなきゃいけないものもあったり)
- webpack比較でビルド速度が遅い
