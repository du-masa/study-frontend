# フィルタ

フィルタとはテキストに対してフォーマット処理をするために使用します。
例えば、テキストを全て大文字にするなど。

フィルタは登録することで使用できます。

# フィルタの実装

今回は、テキストの文字数が一定数を超えたら省略するというフィルタを作ってみましょう。

## フィルタの登録

まずは、フィルタの登録をします。
フィルタの登録はグローバル登録とローカル登録があります。

### グローバル登録

グローバル登録をすることでアプリケーション内のどの要素からも登録したフィルタを呼び出すことができます。

グローバル登録は `Vue.filter` メソッドを使います。 `Vue.filter` メソッドは引数を２つとります。

1つ目は、フィルタ名を文字列で指定します。2つ目は実際の処理を関数で指定します。

```js
// "truncate" という名前のディレクティブを登録する
Vue.filter('truncate', (value) => {
    // do something
})
```

※グローバル登録は、 `new Vue` で初期化する前に行う必要があります。

### ローカル登録

ローカル登録は、そのコンポーネント内のみで使用できるフィルタを登録します。
コンポーネントの `filters` オプションで登録できます。

```js
// "truncate" という名前のディレクティブをローカル登録する
filters: {
    truncate (value) {
        // do something
    }
}
```

## 登録したフィルタ使い方

登録したフィルタは mustache 展開と v-bind 式、2 つの場所で使用できます。
フォーマット化したいテキストを “パイプ(‘|’)” 記号 で繋いで使用します。

```html
<p> {{ 'text' | truncate　}} </p> <!-- mustache展開 -->

<p v-bind:alt="'text' | truncate"></p> <!-- v-bind式 -->
```

## フィルタの引数

フィルタの処理側の関数で受け取る引数について説明します。

第1引数は、フォーマットの対象となるテキストになります。
第2引数以降は、任意の引数を指定した場合に受け取ります。

```html
<p> {{ 'text' | truncate('arg1', 'arg2') }} </p>
```

```js
Vue.filter('truncate', (value, arg1, arg2) => {
    console.log(value) // 'text'が表示される
    console.log(arg1) // 'arg1'が表示される
    console.log(value) // 'arg2'が表示される
})
```

## テキストの省略処理を行う

それでは、実際の処理を書いていきましょう。
今回の処理は、テキストの長さを比較してそれ以上だったら、省略するということを行います。

```js
Vue.filter('truncate', (value) => {
    const length = 100
    const omission = '...' // 省略した時の記号

    if (value.length < length) {
        return value
    } else {
        return value.substring(0, length) + omission
    }
})
```

上記の例では、100文字未満だったら何も処理せずそのままテキストを返し、100文字以上の場合は省略して、省略用の記号を添えて返します。

※フィルタは必ずテキストを戻り値として返す必要があります。

## 引数を使って柔軟なフィルタにする

フィルタに引数を追加して、省略する長さや省略記号を変更できるようにして柔軟なフィルタにしましょう。

```html
<p> {{ 'Lorem ipsum dolor sit amet consectetur adipisicing elit. Saepe voluptatum et molestiae maxime ducimus veniam laudantium magni a omnis nulla tempore atque, quam totam ex aperiam soluta quos! Molestias animi aut pariatur distinctio incidunt iste, blanditiis ab harum repellendus ex corporis et excepturi ipsum, sapiente expedita nam mollitia? Maxime totam eligendi assumenda
' | truncate( 50, '...more') }} </p>
```


```js
Vue.filter('truncate', (value, length = 100, omission = '...') => {

    if (value.length < length) {
        return value
    } else {
        return value.substring(0, length) + omission
    }
})
```

上記の例ではフィルタ引数として、省略の境目となる文字数の数値と、省略記号を受け取れるようにしてます。デフォルト引数も指定することで引数を指定しなくてもフィルタが機能するようにしています。