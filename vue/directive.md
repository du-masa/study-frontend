# ディレクティブ

ディレクティブとは `v-` から始まるvue.jsの特別なHTML属性値です。
vue.jsにはあらかじめいくつかの便利なディレクティブが用意されています。(`v-class`, `v-if`, `v-for` など)

それらを利用して、私たちは面倒なDOM操作を行わずとも動的にDOMの変更を行うことができます。

## カスタムディレクティブ

vue.jsでは、用意されていない任意のディレクティブを作成することができます。それをカスタムディレクティブと呼びます。

既存のディレクティブだけでは実現できないDOM操作が必要になった時に、カスタムディレクティブとして用意しておくと、便利です(特に汎用性の高いDOM操作)

# カスタムディレクティブの実装

今回は実装例として、画像が404エラーの時にあらかじめ用意した代替の画像を表示させるディレクティブを作りましょう。

## ディレクティブの登録

まずは、ディレクティブの登録をします。
ディレクティブの登録はグローバル登録とローカル登録があります。

### グローバル登録

グローバル登録をすることでアプリケーション内のどの要素からも登録したディレクティブを呼び出すことができます。

グローバル登録は `Vue.directive` メソッドを使います。 `Vue.directive` メソッドは引数を２つとります。

1つ目は、ディレクティブ名を文字列で指定します。2つ目は実際の処理をまとめたオブジェクト(directive definition objectと言います)を指定します。

```js
// "imageError" という名前のディレクティブを登録する
Vue.directive('imageError', {
    inserted (el, binding, vnode) {
        // do something
    }
})
```

`inserted` メソッドについては後述しますが、上記のように登録することでグローバルにディレクティブを登録することができます。
※グローバル登録は、 `new Vue` で初期化する前に行う必要があります。

### ローカル登録

ローカル登録は、そのコンポーネント内のみで使用できるディレクティブを登録します。
コンポーネントの `directives` オプションで登録できます。

```js
// "imageError" という名前のディレクティブをローカル登録する
directives: {
    imageError: {
        // ディレクティブ定義
        inserted (el, binding, vnode) {
            // do something
        }
    }
}
```


### 登録したディレクティブ使い方

登録したディレクティブは `v-登録したディレクティブ名` でhtmlに指定することができます。

```html
<img v-image-error>
```

## フック関数

ディレクティブの実際の処理はフック関数と呼ばれるvue.jsがあらかじめ用意した関数内で行います。
どのフック関数を選ぶかはディレクティブが呼ばれるタイミングによって決めます。


```js
// フック関数の動作テスト
Vue.directive('imageError', {
    bind (el, binding, vnode) {
        console.log('bind')
    },
    inserted (el, binding, vnode) {
        console.log('inserted')
    },
    update (el, binding, vnode) {
        console.log('update')
    },
    componentUpdated (el, binding, vnode) {
        console.log('componentUpdated')
    },
    unbind (el, binding, vnode) {
        console.log('unbind')
    }
})

new Vue({
    el: '#app'
});
```

### bind
ディレクティブと要素が紐づいた時に一度だけ呼ばれます。初期化時

### inserted
ディレクティブが紐づいている要素が親要素に挿入された時に呼ばれます。

### update
ひも付いた要素を抱合しているコンポーネントの VNode(データ等)) が更新される度に呼ばれます。

### componentUpdated
抱合しているコンポーネントの VNode と子コンポーネントの VNode が更新された後に呼ばれます。

### unbind
ディレクティブがひも付いている要素から取り除かれた時に 1 度だけ呼ばれます。

### フック関数を選定する

今回実装するディレクティブは、要素と紐づいた時に一度だけ呼ばれれば良いので、 `bind` 関数を使います。

```js
// imageErrorディレクティブでbindフック関数を使用する。
Vue.directive('imageError', {
    bind (el, binding, vnode) {
        // do something
    }
})

```

## ディレクティブフック引数

フック関数は特定の引数を4つ受け取ります。

### el
ディレクティブが紐づいている要素そのものです。この引数を使ってDOM操作を直接行います。

### binding

いくつかの情報を持ったオブジェクトです。

例えば、ディレクティブの属性値は `binding.value` で参照できます。
その値を使って処理の出し分けや計算に使用することができます。

```html
<!-- 属性値を指定する -->
<img v-image-error="imageErrorValue">
```

```js
// binding.valueを参照する
Vue.directive('imageError', {
    bind (el, binding, vnode) {
        console.log(binding.value) // => imageErrorValue
    }
})

```

プロパティの種類が多いので全てのプロパティは下記を参考にしてください。

[bindingのプロパティ一覧](https://jp.vuejs.org/v2/guide/custom-directive.html#%E3%83%87%E3%82%A3%E3%83%AC%E3%82%AF%E3%83%86%E3%82%A3%E3%83%96%E3%83%95%E3%83%83%E3%82%AF%E5%BC%95%E6%95%B0)


### vnode
vue.jsによって生成された仮想nodeです。`vnode` を使うことで仮想DOMにアクセスすることもできるのできます。


## 画像エラー時の処理を実装する

それでは、実際の処理を書いていきましょう。
imgタグで画像の読み込みに失敗した時、 [`onerror` イベント](http://alphasis.info/2013/09/javascript-dom-event-onerror/)が発生します。

このイベントを使って代替画像を指定します。


```js
Vue.directive('imageError', {
    bind (el, binding, vnode) {
        
        el.onerror = () => {
            el.setAttribute('src', 'https://placehold.jp/150x150.png')
        }
    }
})
```

`el` 引数はディレクティブを指定したimgタグ自身になります。
`onerror`イベントが起きたら、imgタグに対して`setAttribute`で代替の画像を指定しています。

``` html
<div>
    <h2>存在する画像を読み込む</h2>
    <img src="./image/demo-1.png" v-image-error>
</div>
<div>
    <h2>存在しない画像を読み込む</h2>
    <img src="./image/demo-2.png" v-image-error>
</div>
```

imgタグで指定した画像が存在する場合は、その画像が表示されます。
反対に存在しない場合は、カスタムディレクティブで指定した画像が表示されます。

## 表示する場所に応じて代替画像を変更する

これまでの実装では、どのimgタグに対しても同じ代替画像が指定されます。これをより汎用的にするには、ディレクティブの属性値に応じて代替画像を切り替えます。

``` html
<div>
    <h2>代替画像1を読み込む場合</h2>
    <img src="./image/demo-2.png" v-image-error="alternative1">
</div>
<div>
    <h2>代替画像2を読み込む場合</h2>
    <img src="./image/demo-2.png" v-image-error="alternative2">
</div>
<div>
    <h2>属性値を何も指定しない場合</h2>
    <img src="./image/demo-2.png" v-image-error>
</div>
```

```js
Vue.directive('imageError', {
    bind (el, binding, vnode) {

        let src
        switch (binding.value) {
            case 'alternative1':
                src = 'https://placehold.jp/100x100.png'
                break
            case 'alternative2':
                src = 'https://placehold.jp/150x150.png'
                break
            default:
                src = 'https://placehold.jp/200x200.png'
                break
        }
        
        el.onerror = () => {
            el.setAttribute('src', src)
        }
    }
})
```

上記の例では、前述した`binding.value`の値を使って出し分けを行なっています。それぞれサイズの違う代替画像が読み込まれます。


これにより、アプリケーション全体でimgタグにエラー時の代替画像の設定を共有処理として用意できます。これがディレクティブのメリットです。