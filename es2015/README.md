# ES2015-2018

## ECMAScriptについて

ECMAScriptとはJavascriptの標準化された仕様です。
各ブラウザベンダーはECMAScriptの仕様を元にブラウザエンジンでJavaScriptとして実装します。

2009年にはECMAScript5(ES5)がリリースされたのち、
2015年にECMAScript6(ES6)がリリースされました。

ECMAScriptのバージョンは、メジャーバージョンで呼ばれてましたが、
バージョンのリリースサイクルが1年ごとになり、西暦をつけた形で呼ばれるようになりました。

例: ES6 => ES2015

その年に策定された仕様が`ES20**`としてリリースされます。
現在はES2018(ES9)までリリースされています。

策定のプロセスについてはこちらにわかりやすくまとめてあります。
[ECMAScriptとJavaScriptの関係 -Code Grid -](https://app.codegrid.net/entry/ecmascript-1)

## 対応状況

ECMAScriptで仕様が策定されたからと言って、すぐにブラウザで使えるようになるわけではありません。
ブラウザ側が対応するまでは使えません。

現在、どの仕様が対応しているかを確認するには対応テーブルを確認します。

[compatibility table](http://kangax.github.io/compat-table/es6/)


## 環境設定

ブラウザが未対応だけども、使いたい仕様がある場合はコードをトランスパイルする必要性があります。
[abel](https://babeljs.io/)もしくは[TypeScript](https://www.typescriptlang.org/)を使って行われることが多いと思います。

今回は、Babelを使ってトランスパイルします。

### インストール

```bash

$ npm i -D @babel/core @babel/cli @babel/preset-env

```

- [@babel/core](https://github.com/babel/babel/tree/master/packages/babel-core)

babel本体です。

- [@bable/cli](https://github.com/babel/babel/tree/master/packages/babel-cli)

babelコマンドを使えるようにします。

- [@bable/preset-env](https://github.com/babel/babel/tree/master/packages/babel-preset-env)

ES20**の仕様をES5(デフォルト設定だと)に変換してくれるプラグイン集

### .babelrc

.babelrcはBabelの設定ファイルです。
どのpresetを使うかや、トランスパイル後のESのバージョン指定などできます。
今回は、`@bable/preset-env`を使う設定を行います。

```json
{
  "presets": ["@babel/preset-env"]
}
```

### コマンド設定

package.jsonに`@bable/cli`のコマンドを設定します。

```json
{
  "scripts": {
    "build": "babel assets -w -d public"
  }
}
```

上記設定では、`assets`ディレクトのjsファイルを対象にトランスパイルし`public`ディレクトリに出力するように設定しています。
`-w`オプションをつけることで変更を感知してくれます。

```bash

$ mkdir assets && touch assets/main.js
$ npm run build

```

上記設定で、`public`ディレクトリに`main.js`が出力されるはずです。


## ES2015-2018 仕様紹介

### const/let

従来、変数宣言は`var`がありましたが、ES2015で`const`と`let`での宣言が追加されました。

`var`との違いは`let`が**再宣言が不可能**、`const`は**再宣言と再代入が不可能**になります。

これにより、変数の二重定義や予期せぬ再代入を防ぐことができます。

```js

var val1 = 'val1'
val1 = 'val1_1' // 再代入可能
var val1 = 'new_val1' //再宣言可能


let val2 = 'val2'
val2 = 'val2_1' // 再代入可能
let val2 = 'new_val2' //再宣言不可


const val3 = 'val3'
val3 = 'val3_1' // 再代入不可
const val3 = 'new_val3' //再宣言不可

```

また、`let/const`共にブロックスコープを持つことができます。
これにより変数の影響範囲を小さくできます。

```js

var val1 = 'val1'

if (true) {
    var val1 = 'val1_1'
    console.log(val1) // => val1_1
}

console.log(val1) // => val1_1

let val2 = 'val2'

if (true) {
    let val2 = 'val2_1'
    console.log(val2) // => val2_1
}

console.log(val2) // => val2

const val3 = 'val3'

if (true) {
    const val3 = 'val3_1'
    console.log(val3) // => val3_1
}

console.log(val3) // => val3
```

もはや`var`宣言を使う必要性はなく、再代入が必要な変数は`let`、それ以外は`const`を使うと良いでしょう。


### テンプレート文字列

JavaScriptで文字列を扱う際は、"(ダブルクオート)か'(シングルクオート)を使います。
そこに新しく`(バッククオート)を使い文字列を扱えるようになりました。
バッククオートで囲んだ文字列を`テンプレート文字列`と言います。

従来の文字列との違いは、変数展開や改行がそのまま反映ができるようになりました。


#### 変数展開

これまで、変数と文字列を結合するには `+` で結合する必要がありました。
しかし、バッククオートで囲むことで変数を展開して結合できます。
展開するには変数を`${}`で囲います。

```js

const name = "taro"

const str1 = "My name is " + name // My name is taro

const str2 = `My name is ${name}` // My name is taro

```

１つの変数だけでなく演算も展開することができます。

下記の例では三項演算子の結果を展開してます。

```js

const name1 = "taro"
const name2 = "emi"
const gender = "male"

const str1 = "My name is " + gender === "male" ? name1 : name2// My name is taro

const str2 = `My name is ${gender === "male" ? name1 : name2}` // My name is taro

```

### 改行

文字列に改行を含めるには、改行コード`\n`を明示的に含める必要性がありました。
テンプレート文字列では改行を入れるだけでそのまま改行を含めることができます。

```js
const title = "タイトル"

const html1 =
  "<div>\n" +
    "<h1>" + title "</h1>\n" +
  "</div>" // =>
  // <div>
  //   <h1>タイトル</h1>
  // </div>

const html2 =
  `<div>
    <h1>${title}</h1>
  </div>` // =>
  // <div>
  //   <h1>タイトル</h1>
  // </div>
```

だいぶシンプルに書くことができます。
改行だけでなくタブやスペースのそのまま文字列として扱われます。


## アロー関数

アロー関数は、関数の定義を省略して書くことができます。
`function`は`=>`に置きかわり短く書くことができるのが特徴です。
また戻り値を返す`return`の省略や、`this`の参照が従来の関数と異なります。

### 基本構文

```js

const func1 = function () {
  return 1 + 1
}

const func2 = () => {
  return 1 + 1
}

func1() // 2
func2() // 2

```

`function`が`=>`に置きかわり`()`が先頭にきます。


### 引数が1つの場合の()省略

引数が1つの場合は`()`を省略できます。

```js

const func1 = function (arg) {
  return arg + 1
}

const func2 = arg => {
  return arg + 1
}

func1(1) // 2
func2(2) // 3

```

引数が2つ以上の場合は省略できません。

```js

const func1 = function (arg1, arg2) {
  return arg1 + arg2
}

const func2 = (arg1, arg2) => {
  return arg1 + arg2
}

func1(1, 1) // 2
func2(2, 1) // 3

```

### returnの省略

関数本体の中身が`return`1文のみの場合は、`return`と`{}`省略できます。

```js

const func1 = function (arg) {
  return arg + 1
}

const func2 = arg => arg + 1

func1(1) // 2
func2(2) // 3

```

だいぶシンプルになりました。
この書き方は配列の`.map`や`filter`メソッドなどを使うときに使うとコードが見やすくなります。

```js

const array = [3, 4, 2, 5, 10]

// 配列の各要素を4倍にして、15以上だけを抜き出した配列を生成
const parsedArray1 = array.map(function(i) {
  return i * 4
}).filter(function(i) {
  return i >= 15
})

// 省略なし
const parsedArray2 = array.map((i) => {
  return i * 4
}).filter((i) => {
  return i >= 15
})

// 省略あり
const parsedArray3 = array.map(i => i * 4).filter(i => i >= 15)

```

### thisの扱い

従来の関数では、本体内にある`this`は実行時に決定します。
例えば、メソッドであればそのメソッドを持っているオブジェクトが`this`になり、
グローバル関数であれば、`this`は`window`オブジェクト(グローバルオブジェクト)になります。

```js

const func = function() {
  console.log(this)
}

const obj = {
  func: func
}

func() // thisはWindow
obj.func() // thisはobj

```

JavaScriptでの`this`の扱いの詳細はこちらをご覧ください。
[JavaScriptの「this」は「4種類」？？](https://qiita.com/takeharu/items/9935ce476a17d6258e27)


`function`を使って関数を生成すると`this`の扱いで仕様上面倒なことがあります。
コールバックに指定する関数は、オブジェクトに紐づいているわけでないので`this`はグローバルオブジェクト(strictモードだと`undefined`)になります。

そのため、`function`を使って`this`を確定(=束縛)したい場合は工夫が必要です。

下記の例は`axios`を使って非同期通信をして取得した内容を`obj`オブジェクトの`apiData`の値に代入しています。

```js
import axios from 'axios'

const obj = {
  apiData: [],
  fetch() {
    const self = this
    axios.get('path/top/endpoint').then(function(res) {
      self.apiData = res.data
    })
  },
  failFetch() {
    axios.get('path/top/endpoint').then(function(res) {
      this.apiData = res.data // <= これだとthisがwindowになるのでwindow.apiDataにapiのデータを代入することになる
    })
  }
}

obj.fetch()
```

例では、`fetch`メソッド内で`this`を`self`変数に代入しています。
そして、`axios`で取得したデータを受け取るコールバック関数内で`self`変数を使って`apiData`にアクセスしています。
このようにしないでコールバック関数で`this`を参照するとそれは`window`オブジェクトになるため、`obj.apiData`でなく`window.apiData`に代入することになってしまいます。


このコールバック関数をアロー関数に置き換えるとどうなるでしょうか。

```js
import axios from 'axios'

const obj = {
  apiData: [],
  fetch() {
    axios.get('path/top/endpoint').then(res => {
      this.apiData = res.data
    })
  },
}

obj.fetch()
```

コールバックにアロー関数を使うと、上記のようになります。`this`は変数に代入することなくそのままコールバック関数の中で参照しています。

これは、アロー関数が`this`が何であるかの確定を**実行時ではなく宣言時で行う**からです。

これによりコールバック関数内でも`this`参照が扱いやすくなります。

#### イベントのコールバック関数の注意点

イベントでのコールバック関数でアロー関数を使うと、`this`が束縛されているため実行時のDOM要素を取得ができなくなるので注意が必要です。

例えば、jQueryでクリックイベントを設定して、クリックされた要素に対して`class`を付与したい場合があるとします。

```js
import $ from 'jquery'

$('button').on('click', () => {
  $(this).addClass('class') // => 'this'は'button'じゃなくてwindowオブジェクトになる。
});

```

上記の場合、`this`は`window`オブジェクトになってしまうので、想定した動きになりません。

対策としては、`function`を使って書くか、アロー関数の場合は`event`オブジェクトを使ってDOM要素を取得します。

```js
import $ from 'jquery'

// functionを使う
$('button').on('click', function() => {
  $(this).addClass('class') 
});

// eventオブジェクトを使う
$('button').on('click', event => {
  $(event.target).addClass('class') // event.targetでイベントが発生したDOM要素を取得できる
});

```

## 分割代入

分割代入は、配列やオブジェクトからデータを個別に変数に代入することができます。

従来、変数代入は個別に１つ１つ行わなくてはいけませんでした。

```js

const a = 1
const b = 2
const c = "c"
const b = {b: 1}

console.log(a, b, c, d) // 1, 2, "c", {b: 1}

```

これに変わって分割代入を使うことで効率よく代入できます。

```js

const [a, b, c, d] = [1, 2, "c", {b: 1}]

console.log(a, b, c, d) // 1, 2, "c", {b: 1}

```

代入される側の配列に変数名を格納します。代入する側のインデックスを同じ変数に値が格納されていきます。
インデックスで代入先が決まるので、順番を意識する必要があります。

オブジェクトも分割代入できます。

```js
const obj {
  num: 1,
  str: "str"
}

const { num, str } = obj

console.log(num, str) // 1, "str"

```

オブジェクトの場合は、代入先の変数名に代入元のオブジェクトのプロパティ名を指定します。  
変数名とプロパティ名を紐づけることで値が変数に代入されます。


### 使用例

#### 値入れ替え

変数Aと変数Bの値を入れ替えたいときに使うと便利です。

従来だと、一時的に値を退避する変数をもう一つ用意する必要がありましたが、分割代入を使えばその必要性はありません。


```js

let a = 'a'
let b = 'b'

// 従来だと・・・
let temp = b
b = a
a = temp

let c = 'c'
let d = 'd'

// 分割代入だと
[d, c] = [c, d]

```


#### オブジェクトのプロパティの一部を取得

あるオブジェクトから一部のプロパティの値のみ取得したい時に使うと便利です。

例えば、`axios`で取得したレスポンスはオブジェクトになっています。実際のレスポンスデータは`data`プロパティに入っているため、そこだけ取得することができます。

```js

  // そのまま・・・
  axios.get('path/top/endpoint').then(res => {
    console.log(res.data)
  })

  // 分割代入でデータを受け取る
  axios.get('path/top/endpoint').then({ data } => {
    console.log(data)
  })
```

コールバック関数の引数で受け取っている`{ data }`は `{ data } = res`を行なっていると思ってください。
これで`data`プロパティの中身だけ取得することができます。
