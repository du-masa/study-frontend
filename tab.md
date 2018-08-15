# demo

`demo`ディレクトリにjqueryとVue.jsで実装したデモがあります。
こちらを参考に実際に作っていきましょう。




# インストールと読み込み

## インストール
npmを使ってvue.jsをインストールします。

```bash
# bash
$ npm install vue
```

## 読み込み
インストールしたVue.jsを`assets/tab.js`で読み込みましょう。

```tab.js
// tab.js

import Vue from 'vue/dist/vue.esm.js';

console.log(Vue);
```

npmでvue.jsをインストールした場合は、幾つかのVue.jsの本体ファイルが存在します。

`node_modules/vue/dist`ディレクトリの中を見てみると複数の本体ファイルがあると思います。用途によって使い分けるのですが、今回は、`vue.esm.js`を使います。

詳しくは公式サイトに書かれています。
[さまざまなビルドについて](https://jp.vuejs.org/v2/guide/installation.html#%E3%81%95%E3%81%BE%E3%81%96%E3%81%BE%E3%81%AA%E3%83%93%E3%83%AB%E3%83%89%E3%81%AB%E3%81%A4%E3%81%84%E3%81%A6)

## ビルド

webpackを使ってビルドしてみましょう。
あらかじめwebpackの設定とpackage.jsonにコマンドを用意しているので、それを使います。

```bash
# bash
$ npm i
$ npm start
```

`public`ディレクトリに`js`と`css`のディレクトリが生成されその中にそれぞれファイルができます。ブラウザで`public/tab.html`を開いて、デベロッパーツールのコンソールに、Vueの本体が表示されていれば、問題ありません。

![スクリーンショット 2018-04-18 17.31.07.png](https://qiita-image-store.s3.amazonaws.com/0/83765/9369d857-642d-2fe6-45d8-96d115775ca0.png "スクリーンショット 2018-04-18 17.31.07.png")

この環境を使ってタブを実装していきましょう。


# Vue.jsの初期化処理

## 初期化

Vue.jsを使うにはまず、初期化処理を行います。
初期化はVueのインスタンスを作成します。
`assets/tab.js`に下記を追加しましょう。

```tab.js
// tab.js

const vm = new Vue({});

```

これで、Vue.jsを使えるようになります。
この時、Vue関数の引数にjsのオブジェクトを渡します。
このオブジェクトのプロパティは、Vue.jsの方で用意してあり、そのプロパティの役割ごとに
必要な設定をしていきます。
プログラムで使用するデータや関数はこのオブジェクトに書いていきます。

## VueのインスタンスとDOM要素の紐付け

elプロパティを使って
VueのインスタンスとDOM要素を紐付けます。

`public/tab.html`が下記のようになっていると思います。

```tab.html
<!-- tab.html -->

<div class="tabContent" id="tabContent">
<!-- タブの中身-->
</div>

```

ここのHTMLにある`id="tabContent"`を使ってVueインスタンスと紐付けます。

```tab.js
// tab.js

const vm = new Vue({
  el: '#tabContent'
});
```

上記のようにelプロパティにcssのプロパティ指定をすることで、紐付けができます。
*基本的にIDで指定します。class名で指定するとHTML内に同じclass名が複数あると、htmlの最後に記述されたDOMが対象になります。

この指定により、`<div class="tabContent" id="tabContent">`をルートとしてそれ以下のDOM要素がVue.jsの対象となりました。

# データを扱う

プログラムで使用するデータはdataプロパティに指定します。

試しに１つ値を設定してみましょう。

```tab.js
// tab.js

const vm = new Vue({
  el: '#tabContent',
  data: function() {
    return {
      text: 'テキスト'
    }
  }
});
```

まず、dataプロパティには関数を指定します。その戻り値をオブジェクトにして、
その各プロパティを値として扱います。
上記の例では、`text`という名前のプロパティ(変数)を作り、`テキスト`という文字列を設定(代入)しています。

dataプロパティに指定した値は、HTML内や属性値、後述するメソッド内で参照することができます。


試しに、HTMLで表示してみましょう。

```tab.html
<!-- tab.html -->

<div class="tabContent" id="tabContent">
  {{text}}
<!-- タブの中身-->
</div>

```

HTML内でテキストとしてデータを表示するには、二重中括弧`{{}}`で囲みます。
これで、ブラウザで見ると`テキスト`という文字が表示されます。

試しに、`テキスト`という文字を他の文字に変更してみてください。
変更した文字が表示されると思います。

Vue.jsで管理しているデータをHTMLで扱えることがわかると思います。

## どのタブが選択されているかという状態を管理する `tab`

では、このdataプロパティを使ってタブの状態を管理しましょう。
今回は、どのタブが現在選択されているかを示す文字列を指定します。


```tab.js
// tab.js

const vm = new Vue({
  el: '#tabContent',
  data: function() {
    return {
      activeTab: 'tab1'
    }
  }
});
```

`activeTab`というデータを用意します。
そして、初期状態は、１つ目のタブが選択されていると思うので`tab1`という文字列を設定しました。

# タブに紐づくコンテンツを制御する

タブは、クリックされた時にそれに対応するコンテンツを表示、それ以外を非表示という
処理を行います。

jqueryのデモを見てもらうと、
タブに紐づくコンテンツの表示非表示切り替えは、押されたタブから対象となるコンテンツのIDを取得してそのIDのDOM要素に表示用のcssクラス名を付与しています。

Vue.jsの場合は、cssではなく`v-if`ディレクティブを使って表示・非表示を切り替えましょう。

## ディレクティブ

ディレクティブとは、Vue.jsが用意しているHTMLのカスタム属性です。
このディレクティブことによって、柔軟にUIの状態を扱えるようになります。

## v-if(v-show)

属性値の条件によってDOMの表示・非表示を行います。
条件がtrueの場合は、表示。falseの場合は非表示にします。

```html
<div v-if="true">
これは表示</div>

<div v-if="false">
これは非表示
</div>
```

`v-if`と`v-show`は同じような機能を果たしますが、非表示時の扱いが若干違います。
`v-if`はDOMから要素を取り除くきます。`v-show`はcssの`display:none`で非表示にします。

## v-ifを使ってタブに紐づくコンテンツを制御する。`tab`

`tab.html`の`<div class="tabMain"></div>`の中を変えていきましょう。

```tab.html
<!-- tab.html -->

<div class="tabMain">
  <section class="tabMain__section" v-if="activeTab === 'tab1'">
    Lorem ipsum dolor sit amet consectetur adipisicing elit. Nam, ut.
  </section>
  <section class="tabMain__section" v-if="activeTab === 'tab2'">
    Lorem ipsum dolor, sit amet consectetur adipisicing elit. Voluptas, cum dolorem quisquam consequatur ex placeat ratione soluta nulla tenetur quos!
  </section>
  <section class="tabMain__section" v-if="activeTab === 'tab3'">
    Lorem ipsum dolor sit amet consectetur adipisicing elit. Alias, atque ipsum aspernatur et sit quod accusantium corporis tempora ea quia dignissimos nisi fugit fuga saepe impedit culpa similique eum illum pariatur! Ea corrupti eius voluptates error eum assumenda labore aperiam!
  </section>
  <section class="tabMain__section" v-if="activeTab === 'tab4'">
     Lorem ipsum dolor sit amet consectetur adipisicing elit. Modi doloremque eveniet ipsum possimus blanditiis consequatur inventore aliquid nobis. Unde alias ullam nulla eveniet, nihil accusamus rem voluptatem iure corporis fuga commodi eos temporibus, error minus, cupiditate laborum dignissimos reiciendis eligendi autem! Illum, sit quasi deleniti vitae consequatur sunt? Dolores, officiis.
  </section>
</div>
```

変更点下記です。

1. 4つの`section`タグから `id`属性を削除及び、1つ目の`section`タグからclass名`tabMain__section--active`を削除
2. `v-if`を使ってその属性値内で表示・非表示の条件を判定

2のところで`v-if`を使ってます。ここでは、先ほどdataプロパティで指定した`activeTab`を使ってます。この`activeTab`の値がそれぞれの`section`タグで指定した文字列と等しいかどうか判定し、等しかった場合`true`になるので表示されるということです。

先ほど指定した`activeTab`の値の値は`'tab1'`でしたので、今回は1つ目の`section`タグが`true`になり表示、それ以外は`false`になるので非表示となります。


次に、jqueryで使っていたcssの記述が不要なので、削除します。

`assets/scss/_tab.scss`の30-38行目を削除します。

```_tab.scss
/* _tab.scss 30-38行目を削除 */

.tabMain {
  padding: 20px;
}

```

この状態でブラウザを見ると、１番目の`section`タグが表示されていると思います。

# タブのスタイルを変える

選択中のタブは、選択中をユーザーに伝えるため、そうでないタブとスタイルが違います。
その処理を`v-bind:class`ディレクティブを使って行います。

## v-bindディレクティブ

`v-bind`は動的に属性値を持たせることができます。HTMLで用意されている属性はもちろん、カスタム属性も使えます。
属性は`v-bind`の後ろに`:属性名`という形で使用します。

例:
id => `v-bind:id`
class => `v-bind:class`


`v-bind:class`を使うと下記のような形になります。

```html
<div v-bind:class="{'active': isActive }">
<!-- isActive が true なら class="active"が追加DOM要素になる -->
</div>


<script>
const vm = new Vue({
  data: function() {
    return {
      isActive: true
    }
  }
});
</script>

```

`v-bind:class`は属性値にオブジェクトを渡します。(class属性は複数の値を持つため)
オブジェクトのプロパティ名には付与するクラス名、プロパティ値にはそのクラスを付与する条件を書きます。`true`になれば付与、`false`になれば付与しないという形になります。

今回の場合、dataプロパティに指定した`isActive`が`true`か`false`で`active`が付与されるかされないかを指定しています。

## v-bind:classを使って選択中のタブのスタイルを変える `tab`

選択中のタブのスタイルを動的にclassを付与して変更しましょう。

`tab.html`の`<ul class="tab"></ul>`の中を変えていきましょう。

```tab.html
<!-- tab.html -->

<ul class="tab">
  <li class="tab__item">
    <div v-bind:class="{'tab__target--active': activeTab === 'tab1'}" class="tab__target">tab1</div>
  </li>
  <li class="tab__item">
    <div v-bind:class="{'tab__target--active': activeTab === 'tab2'}" class="tab__target">tab2</div>
  </li>
  <li class="tab__item">
    <div v-bind:class="{'tab__target--active': activeTab === 'tab3'}" class="tab__target">tab3</div>
  </li>
  <li class="tab__item">
    <div v-bind:class="{'tab__target--active': activeTab === 'tab4'}" class="tab__target">tab4</div>
  </li>
</ul>

```

変更点下記です。

1. 4つの`<div class="tab__target">`タグから`data-target`を削除及び1つ目の`div`タグから`tab__target--active`クラスを削除
2. `v-bind:class`を使って`tab__target--active`クラスを条件付で付与する記述を書く。

`tab__target--active`クラスをつけることによって選択中のタブのスタイルになります。
このクラスを条件に応じて付与することで、選択中のタブのみスタイルがつくかたちになります。

条件は、先ほどタブに紐づくコンテンツの表示・非表示に使ったものと同じです。
`activeTab`の値は`'tab1'`ですので、１つ目のタブのスタイルが選択中のものになります。


# クリックされたら表示を切り替える

ここまで、初期状態の表示はできました。
ただこのままでは、タブをクリックしても何も変わりません。
その処理を入れましょう。

## v-onディレクティブ

`v-on`ディレクティブは、DOM上で発生するイベントをハンドリングすることができます。

`v-on`の後ろに`:イベント名`を指定してハンドリングします。

例:
click => `v-bind:click`
blur => `v-bind:blur`

属性値に処理(関数、メソッド)を与えることでイベントが発生した際の振る舞いを指定できます。

　
```html
<div v-on:click="なんらかの処理"></div>
</div>
```

## タブにclickイベントをつける `tab`

各タブに`v-on:click`を使ってクリックイベントをハンドリングしましょう。`tab`

```tab.html
<!-- tab.html -->

<ul class="tab">
  <li class="tab__item">
    <div v-on:click="なんらかの処理" v-bind:class="{'tab__target--active': activeTab === 'tab1'}" class="tab__target">tab1</div>
  </li>
  <li class="tab__item">
    <div v-on:click="なんらかの処理" v-bind:class="{'tab__target--active': activeTab === 'tab2'}" class="tab__target">tab2</div>
  </li>
  <li class="tab__item">
    <div v-on:click="なんらかの処理" v-bind:class="{'tab__target--active': activeTab === 'tab3'}" class="tab__target">tab3</div>
  </li>
  <li class="tab__item">
    <div v-on:click="なんらかの処理" v-bind:class="{'tab__target--active': activeTab === 'tab4'}" class="tab__target">tab4</div>
  </li>
</ul>

```

各タブに`v-on:click="なんらかの処理"`という属性を追加しました。
これでclickイベントが取れるようになりました。
あとは`"なんらかの処理"`を用意するします。


## メソッドを使う。methodsプロパティ

Vue.jsで処理を指定するには、methodsプロパティを使用します。
methodsプロパティにはVueインスタンスで使用するメソッド群を書いていけます。


```html

<div v-on:click="thisIsMethods">click me</div>

<script>
const vm = new Vue({
  methods: {
    thisIsMethods: function() {
      console.log('run thisIsMethods');
    }
  }
});
</script>
```

上記の例では、methodsプロパティに`thisIsMethods`というメソッドを指定しています。
`thisIsMethods `は`<div v-on:click="thisIsMethods">click me</div>`の`v-on:click`に指定されていて、このDOM要素がクリックされたら処理が走ります。

## メソッドを使って、タブ切り替えをする `tab`

では、メソッドを使ってタブ切り替えを行いましょう。

まずは、メソッドを用意します。

```tab.js
// tab.js

const vm = new Vue({
  el: '#tabContent',
  data: function() {
    return {
      activeTab: 'tab1'
    }
  },
  methods: {
    setActiveTab: function() {
      
    }
  }
});
```
`setActiveTab`というメソッドを用意しました。
とりあえず、処理の中身は空にしておきましょう。

今度は、先ほどHTMLで指定した`v-on:click="なんらかの処理"`を`v-on:click="setActiveTab"`に変更します。

### どうやってタブ切り替えを実現するか

`setActiveTab`メソッドの中身ですが、`activeTab`の値を変更を行います。
`activeTab`は、どのタブが選択状態かを示す値をもたせていました。
HTMLでも`v-if`や`v-bind:class`を使って、`activeTab`の値を見ることでUIの状態を決めていました。

なので、`activeTab`の値を変更を行います。

クリックされたタブから、どのタブであるかという情報を受け取り、それを`activeTab`に新しく代入してあげます。

まずは、HTML側からどのタブが押されたかという情報を送ります。

```tab.html
<!-- tab.html -->

<ul class="tab">
  <li class="tab__item">
    <div v-on:click="setActiveTab('tab1')" v-bind:class="{'tab__target--active': activeTab === 'tab1'}" class="tab__target">tab1</div>
  </li>
  <li class="tab__item">
    <div v-on:click="setActiveTab('tab2')" v-bind:class="{'tab__target--active': activeTab === 'tab2'}" class="tab__target">tab2</div>
  </li>
  <li class="tab__item">
    <div v-on:click="setActiveTab('tab3')" v-bind:class="{'tab__target--active': activeTab === 'tab3'}" class="tab__target">tab3</div>
  </li>
  <li class="tab__item">
    <div v-on:click="setActiveTab('tab4')" v-bind:class="{'tab__target--active': activeTab === 'tab4'}" class="tab__target">tab4</div>
  </li>
</ul>

```

`setActiveTab`メソッドにどのタブかという情報を引数として渡します。

`setActiveTab`メソッドではその引数を受け取って、`activeTab`の値を更新します。

```tab.js
// tab.js

const vm = new Vue({
  el: '#tabContent',
  data: function() {
    return {
      activeTab: 'tab1'
    }
  },
  methods: {
    setActiveTab: function(selectedTabName) {
      this.activeTab = selectedTabName;
    }
  }
});
```

引数で受け取った`selectedTabName`をそのまま`activeTab`に代入します。
これで`activeTab`の値が更新され、それに紐付いたDOMの表示に切り替わります。

*メソッド内でdataプロパティの値を参照するには`this.[データ名]`とします。


# HTMLをシンプルにしてみる

先ほど実装したものは、HTML内に静的に４つのタブを書いていました。
これでもいいのですが、同じようなHTMLを書いていて保守が面倒になります。

例えば、タブを５つにしたい時はHTMLをコピーして値を変えて・・・などの作業が発生します。

この構成をもっとシンプルにしてみましょう。

## v-forディレクティブ

`v-for`ディレクティブはjavascriptの配列やオブジェクトをその要素分繰り返してくれます。


```html

<div v-for="num in [1,2,3,4]">
  {{num}}
</div>
```

このようにすると配列`[1,2,3,4]`を１つ１つ`num`変数に代入して、繰り返し処理をしてくれます。

これをブラウザで見ると下記のようにDOMが生成されます。


```html

<div>
  1
</div>
<div>
  2
</div>
<div>
  3
</div>
<div>
  4
</div>
```


また、`v-for`を使う場合は、合わせて`v-bind:key`という属性を使うことを強く推奨されています。この属性に一意の値を与えることで、Vue.jsがDOMに更新があった際に効率よく再描画を行えるようになります。

`v-bind:key`の指定例は下記になります。

```html

<div v-for="num in [1,2,3,4]" v-bind:key="num">
  {{num}}
</div>
```

今回は一意の値として`num`変数を渡しています。

## タブのHTMLをv-forでシンプルにする `tab`

まずは、HTMLに書いてあるタブ名やテキストをVue.jsのdataプロパティで管理しましょう。

```tab.js
// tab.js

const vm = new Vue({
  el: '#tabContent',
  data: function() {
    return {
      activeTab: 'tab1',
      tabList: [
            {
              title: 'tab1',
              text: 'Lorem ipsum dolor sit amet consectetur adipisicing elit. Nam, ut.'
            },
            {
              title: 'tab2',
              text: 'Lorem ipsum dolor, sit amet consectetur adipisicing elit. Voluptas, cum dolorem quisquam consequatur ex placeat ratione soluta nulla tenetur quos!'
            },
            {
              title: 'tab3',
              text: 'Lorem ipsum dolor sit amet consectetur adipisicing elit. Alias, atque ipsum aspernatur et sit quod accusantium corporis tempora ea quia dignissimos nisi fugit fuga saepe impedit culpa similique eum illum pariatur! Ea corrupti eius voluptates error eum assumenda labore aperiam!'
            },
            {
              title: 'tab4',
              text: 'Lorem ipsum dolor sit amet consectetur adipisicing elit. Modi doloremque eveniet ipsum possimus blanditiis consequatur inventore aliquid nobis. Unde alias ullam nulla eveniet, nihil accusamus rem voluptatem iure corporis fuga commodi eos temporibus, error minus, cupiditate laborum dignissimos reiciendis eligendi autem! Illum, sit quasi deleniti vitae consequatur sunt? Dolores, officiis.'
            },
     ]
    }
  },
  methods: {
    setActiveTab: function(selectedTabName) {
      this.activeTab = selectedTabName;
    }
  }
});
```

`tabList`という配列を用意して、その中にタブごとのオブジェクトを格納しています。
この配列を使ってHTMLで繰り返し処理を行います。

```tab.html
<!-- tab.html -->
<ul class="tab">
  <li class="tab__item" v-for="tab in tabList" v-bind:key="tab.title">
    <div v-on:click="setActiveTab(tab.title)" class="tab__target" v-bind:class="{'tab__target--active': activeTab === tab.title}">
      {{tab.title}}
    </div>
  </li>
</ul>
<div class="tabMain">
  <section class="tabMain__section" v-for="tabMain in tabList" v-if="activeTab === tabMain.title" v-bind:key="tabMain.title">
    {{tabMain.text}}
  </section>
</div>
```

だいぶ短くなりました。
タブとそのコンテンツをそれぞれ`v-for`で繰り返し処理してます。`v-if`や`v-bind:class`の条件部分は、静的テキスト(``tab1``など)を書いてましたが、変数を使って表現できるようになりました。

これで、タブの数を増減させたり、表示する内容を変えたりといった修正がしやすくなったと思います。

# Vue.jsの特徴

Jqueryのように直接DOMを変更するのではなく、あるデータに応じでDOMの描画を制御します。
このデータの値がこうならこうする、そうでなければこうするといった形であらかじめ表示内容とデータが紐づけられているため、データとUIの整合性が取りやすくなります。

また、v-ifやv-forなどのディレクティブがあることによって、処理がシンプルになりコードの見通しが良くなります。(js内で扱うif文やfor文を少なくできる)

# おまけ：json-serverを使ってタブのデータを表示する

json-serverを使ってAPIでJSONを取得して、タブを表示してみましょう。

[json-serverの基本的な使い方はこちら](https://github.com/zap-mueda/json-server/wiki/json-server)

## インストール

json-serverと非同期通信を簡単に行ってくれる`axios`というモジュールをnpmでインストールしましょう。

```bash
# bash
$ npm i -D json-server
$ npm i axios
```


## JSONファイルの用意

ルート直下に`tabList.json`を用意しましょう。

```json
{
  "tabList":[
    {
      "title": "tab1",
      "text": "Lorem ipsum dolor sit amet consectetur adipisicing elit. Nam, ut."
    },
    {
      "title": "tab2",
      "text": "Lorem ipsum dolor, sit amet consectetur adipisicing elit. Voluptas, cum dolorem quisquam consequatur ex placeat ratione soluta nulla tenetur quos!"
    },
    {
      "title": "tab3",
      "text": "Lorem ipsum dolor sit amet consectetur adipisicing elit. Alias, atque ipsum aspernatur et sit quod accusantium corporis tempora ea quia dignissimos nisi fugit fuga saepe impedit culpa similique eum illum pariatur! Ea corrupti eius voluptates error eum assumenda labore aperiam!"
    },
    {
      "title": "tab4",
      "text": "Lorem ipsum dolor sit amet consectetur adipisicing elit. Modi doloremque eveniet ipsum possimus blanditiis consequatur inventore aliquid nobis. Unde alias ullam nulla eveniet, nihil accusamus rem voluptatem iure corporis fuga commodi eos temporibus, error minus, cupiditate laborum dignissimos reiciendis eligendi autem! Illum, sit quasi deleniti vitae consequatur sunt? Dolores, officiis."
    }
  ]
}
```

jsonファイルができたら、json-serverを立ち上げておきましょう。

```bash
#bash
$ npm run jsonServer

```

## jsからAPIを呼び出す

### axiosのインポート

まず、axiosを使えるようにしましょう。

```tab.js
// tab.js

import Vue from 'vue/dist/vue.esm.js';
import axios from 'axios';
```

### axiosによる非同期通信

axiosでは下記のようにAPIを読んで、データを取得します。

```js

axios.get('http://localhost:3000/tabList').then(function(res) {
  console.log(res.data);
}
```

`axios.get`メソッドにAPIのURLを渡すことで、getリクエストをすることができます。 `axios.get`メソッドの戻り値は、Promiseというオブジェクトで、 データの取得に成功したら`then`メソッドを使って、レスポンス内容を取得できます。

レスポンスの中に`data`というプロパティがあり、その中に実際のデータが入っています。(今回で言うとres.data)

この処理をVue.js内で行って、`res.data`をVueインスタンスの`tabList`に渡します。

### 呼び出すタイミング -ライフサイクルフック-

非同期通信は、あらゆるタイミングで呼ぶことができます。画面の初期化時や、ユーザーが何かアクションを起こした時など・・・。

今回は、初期化時と同時にデータを取得します。

その初期化時をVue.jsでどう判定するのかというと、Vue.jsにはライフサイクルフックと言って、初期化時や値が更新された時、など幾つかのタイミングでイベントを発生するようになっています。

何種類かあるのですが、今回は初期化時に発生する`created`メソッドを使います。


```tab.js
// tab.js

const vm = new Vue({
  el: '#tabContent',
  data: function() {
    return {
      activeTab: 'tab1',
      tabList: []
    }
  },
  methods: {
    setActiveTab: function(selectedTabName) {
      this.activeTab = selectedTabName;
    }
  },
  created: function() {
    const self = this;
    axios.get('http://localhost:3000/tabList').then(function(res) {
      self.tabList = res.data;
    });
  }
});
```

`created`メソッドを追加し、その中で非同期通信しています。
返ってきたデータを`tabList`の中に入れています。

dataプロパティの`tabList`は初期値として、空の配列を設定しています。

これで、APIを使ってJSONデータを取得しそれをタブのデータとして使うことができました。
