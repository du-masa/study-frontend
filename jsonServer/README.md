# json server

## リポジトリ

https://github.com/du-masa/json-server

## 概要

[json server](https://github.com/typicode/json-server)は簡単なAPIのモックサーバーを用意することができます。

APIを使ってデータを取得するようなサイトを作る際に、仮にサーバーの機能が完了していなくても、モックデータでフロントエンドの作業を進めることができます。

単純にjsonファイルを取得するだけでなく、リクエストに応じてデータの追加、更新、削除なども行えます。

## インストール

```bash
$ npm i json-server -D

```

*package.jsonが初期化されているのが前提です。


## モックデータを用意

適当なjsonファイルを用意します。

```json
{
  "posts": [
    { "id": 1, "title": "json-server", "author": "typicode" },
    { "id": 2, "title": "api-server", "author": "someone1" },
    { "id": 3, "title": "api-example", "author": "someone2" }
  ],
  "comments": [
    { "id": 1, "body": "some comment", "postId": 1 }
  ],
  "profile": { "name": "typicode" }
}
```

## モックサーバー起動

```bash
$ npx json-server --watch mock.json
```

`json-server`コマンドでjsonのファイル名を指定することで立ち上がります。

`--watch`コマンドをつけることで、起動中にjsonファイルを変更したら更新してくれます。

![スクリーンショット 2018-03-19 19.25.18.png](https://qiita-image-store.s3.amazonaws.com/0/83765/d475daea-16cd-4459-59bf-dc1f9fe7eee9.png "スクリーンショット 2018-03-19 19.25.18.png")

ターミナルには上記のような表示が出ると思います。

`Resources`のところがAPIのURLになります。
URLはjsonファイルのルート直下のメンバー(今回の例で言うと、`posts``comments``profile`)ごとに生成されます。

`http://localhost:3000/posts`にアクセスするとデータが返ってきていると思います。


## javascriptで取得してみる

jsで実際にAPIを叩いてデータを取得してみましょう。

jsからxhr(Ajax)を使うためのモジュールとして`axios`をインストールしましょう。
※ jqueryの`$ajax`メソッドやhtml5の`fetch API`など他のAPIでも大丈夫です。

```bash
$ npm i axios
```


htmlとjsを用意します。

```bash
$ touch index.html index.js
```

htmlは次のようにします。


```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <div id="wrapper"></div>
<script src="./node_modules/axios/dist/axios.js"></script>
<script src="./index.js"></script>
</body>
</html>
```

jsは下記です。

```js

axios.get('/posts').then(function(res) {
	
    const wrapper = document.querySelector('#wrapper');
  
    res.data.forEach(function(data) {
        const newDiv = document.createElement("p");
        const newContent = document.createTextNode(JSON.stringify(data));
        newDiv.appendChild(newContent);
        wrapper.appendChild(newDiv);
    });
    
});
```

簡単な`axios`のajax通信の説明ですが、

`axios.get`メソッドにAPIのURLを渡すことで、getリクエストをすることができます。
`axios.get`メソッドの戻り値は、Promiseというオブジェクトで、 データの取得に成功したら`then`メソッドを使って、レスポンス内容を取得できます。

レスポンスの中に`data`というプロパティがあり、その中に実際のデータが入っています。(今回で言うと`res.data`)


あとは、`res.data`が配列なのでループで回して、htmlにappendしています。


ファイルが用意できたら、再びサーバーを起動してみましょう。

```bash
npx json-server --watch mock.json --static ./
```

`--static`オプションにディレクトリを指定することでそこに配置されたhtmlはhttpで公開する機能を持っています。
今回は、rootディレクトリを指定したので、先ほどの`index.html`は`localhost:3000`でアクセスすることができます。
※ `--static`オプションのデフォルトは`public`ディレクトリになります。

## データの追加／更新／削除

リクエストメソッドを変更することでjsonデータの追加／更新／削除を行えます。

### 追加

```js

axios({
    method: 'POST',
    url:'/posts',
    headers: {
        'Content-Type': 'application/json'
    },
    data: {
        'title': 'react' + Math.round((Math.random() * 10)),
        'author': 'facebook' + Math.round((Math.random() * 10))
    }
}).then(function(res) {

    const wrapper = document.querySelector('#wrapper');

    const newDiv = document.createElement("p");
    const newContent = document.createTextNode(JSON.stringify(res.data));
    newDiv.appendChild(newContent);
    wrapper.appendChild(newDiv);

});

```

リクエストメソッドを`post`にして、追加したいデータを渡してあげることで、`mock.json`にデータが追加されていきます。

レスポンスで返ってくるデータは、追加したデータになります。


### 更新

```js

axios({
    method: 'PUT',
    url:'/posts/1',
    headers: {
        'Content-Type': 'application/json'
    },
    data: {
        'title': 'react' + Math.round((Math.random() * 10)),
        'author': 'facebook' + Math.round((Math.random() * 10))
    }
}).then(function(res) {

    const wrapper = document.querySelector('#wrapper');

    const newDiv = document.createElement("p");
    const newContent = document.createTextNode(JSON.stringify(res.data));
    newDiv.appendChild(newContent);
    wrapper.appendChild(newDiv);

});

```

リクエストメソッドを`put`にします。
それから、APIのURLを`/posts/1`のようにURLの末尾にIDを指定することで、指定IDに一致する特定のデータを対処とできます。

これで、更新対象となるデータを指定できます。

あとは、データの追加と同じです。

レスポンスデータは、更新したデータです。


### 削除

```js

axios({
    method: 'DELETE',
    url:'/posts/4',
    headers: {
        'Content-Type': 'application/json'
    },
    data: {
        'title': 'react' + Math.round((Math.random() * 10)),
        'author': 'facebook' + Math.round((Math.random() * 10))
    }
}).then(function(res) {

    const wrapper = document.querySelector('#wrapper');

    const newDiv = document.createElement("p");
    const newContent = document.createTextNode(JSON.stringify(res.data));
    newDiv.appendChild(newContent);
    wrapper.appendChild(newDiv);

});

```

リクエストメソッドを`delete`にします。
あとは、更新と同じようにURLの末尾に削除するIDを指定します。


## その他

データを取得する際に、フィルタをかけたり、ソートさせたりといった、
SQL文で取得するようなリクエストもできます。

[filter](https://github.com/typicode/json-server#filter)
[sort](https://github.com/typicode/json-server#sort)


## おまけ

大量のモックデータが欲しい場合、手動でjsonファイルを用意すると大変です。
そのため、自動でjsonファイルを生成するスクリプトがあると便利です。

データはランダムでダミーデータをつくってくれる`faker`というモジュールを使います。

```bash
$ npm i faker -D
```

fakerは豊富なAPIがあり、必要となるデータの形を選ぶことができます。
[faker API一覧](https://www.npmjs.com/package/faker#api-methods)


fakerを使ったjsonデータのスクリプトはこちらに用意しました。

[jsonGenerate.js](https://github.com/zap-mueda/json-server/blob/master/jsonGenerate.js)

下記コマンドで、jsonファイルが生成されます。

```bash
$ node jsonGenerate.js
```
