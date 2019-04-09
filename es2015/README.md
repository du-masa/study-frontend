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
[babel](https://babeljs.io/)もしくは[TypeScript](https://www.typescriptlang.org/)を使って行われることが多いと思います。

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
    "dev": "babel assets -w -d public"
  }
}
```

上記設定では、`assets`ディレクトのjsファイルを対象にトランスパイルし`public`ディレクトリに出力するように設定しています。
`-w`オプションをつけることで変更を感知してくれます。

```bash

$ mkdir assets && touch assets/main.js
$ npm run dev

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
$('button').on('click', function() {
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


## デフォルト引数

関数にデフォルト引数を設定できるようになりました。

```js

const calc = (num = 2) => num * 2

calc(10) // 20
calc() // 4

```

デフォルト引数を設定すると引数を渡した場合はその値を受け取り、
引数が渡って来なかった場合はデフォルトで設定している値が引数に代入されます。

引数が複数ある場合にも、デフォルト引数を設定している引数の順番に制約はありません。(エラーにならない)

```js

const calc = (num = 2, num2) => num * num2

calc(10, 2) // 20
calc() // 2 + undefined => return is NaN

```

上記の例だと1番目の`calc`関数の呼び出しは、引数を２つ渡しているのできちんと計算された答えが返ってきます。2番目の`calc`関数の呼び出しは、引数を省略しています。1番目の引数はデフォルト値があるので`2`になりますが、2番目の引数は渡ってきてないので`undefined`になります。
`2 * undefined`を行い、戻り値は`NaN`になります。

このようにJavaScriptではデフォルト引数の順番に制約はありませんが、予期せぬエラーを招く恐れがあるので、通常の引数の後に指定するのが賢明かと思います。

## スプレッド演算子

スプレッド演算子は`...`で表せます。配列展開や可変長引数で使用されます。

### 配列の展開

スプレッド演算子を配列に使うと、カンマ区切りで展開してくれます。

```js

const array = [1, 4, 10, 2]

console.log(...array)
// 上記は下記と同じ
console.log(1, 4, 10, 2)

```

#### 使用例

##### 配列の値を関数の引数に使いたい時

例えば、`Math.max`メソッドは引数の数値の中から最大値を返します。

```js
Math.max(1, 10, 3) // 10
```

ある配列の数値の中の最大値を返したい場合はどうすれば良いでしょう。
従来だと`apply`メソッドを使っていました。

```js
const array = [1, 10, 3]
Math.max.apply(null, array) // 10
```

回りくどくわかりずらい書き方になります。

そこでスプレッド演算子を使うを簡単に展開できます。

```js
const array = [1, 10, 3]
Math.max(...array) // 10
```

##### 配列をコピーしたい時

配列は参照渡しなので、他の変数に代入するだけではコピーはできません。

```js
const array1 = [1,2,3]
const array2 = array1

array2[2] = 4

console.log(array1) // [1,2,4]
console.log(array2) // [1,2,4]
```

配列を他の変数に代入して、配列の中身を変更した場合は代入元の配列も同じように変更されます。

そのため、配列をコピーするには別の方法が必要です。

従来だと配列の`slice`メソッドを使ってました。

```js
const array1 = [1,2,3]
const array2 = array1.slice()

array2[2] = 4

console.log(array1) // [1,2,3]
console.log(array2) // [1,2,4]

```

スプレッド演算子を使ってもコピーできるようになってます。

```js
const array1 = [1,2,3]
const array2 = [...array1]

array2[2] = 4

console.log(array1) // [1,2,3]
console.log(array2) // [1,2,4]

```

`...array1`が展開されて `1,2,3` になり それを`[]`で囲っているので普通の配列宣言のようになります。
そのため、`array1`と`array2`は別の配列を参照することになります。


### 可変長引数

関数で指定した仮引数の数を超えて引数を渡した場合に、配列として受け取ることが可能です。

従来も多く引数を渡した場合は、`arguments`という引数で受け取ることが可能でした。
`arguments`には仮引数も合わせた全ての引数が格納されます。


しかしこの`arguments`は配列ライクなもので完全な配列ではありません。
そのため、配列で使えるメソッドがそのままでは使えないという欠点がありました。


```js

const calc = function(base) {
    const array = [].slice.call(arguments, 1)
    let total = 0
    array.forEach((i) => {
        total += base * i
    })

    return total
}

calc(10, 2, 33)

```

上記の例では、第一引数に渡された値をベースにそれ以外の引数を掛けて足したものを返してます。
ループで使われている`forEach`メソッドは配列のメソッドなので`arguments`は使えません。
また、第1引数は仮引数でで指定されていてループさせたくないので、それを除いた値をもつ配列にコピーしています。


この回りくどい処理は、可変長引数を使えばシンプルに書けます。

```js

const calc = (base, ...array) => {
    let total = 0
    array.forEach((i) => {
        total += base * i
    })

    return total
}

calc(10, 2, 33)

```

第2引数にスプレッド演算子を使った引数を用意することで、第2引数以降に渡ってくる値を配列に格納してくれます。そのため、そのまま配列のメソッドた使えるようになっています。


## Promise

Promiseは非同期処理を扱うための仕組みです。
Promiseを使うことでいつ終わるかわからない非同期処理後の別処理を扱いやすくしてくれます。


### コールバック地獄からの脱出

非同期処理の代表例に`setTimeout`関数があります。  
特定の時間を待ってから指定した処理を行う関数です。

複数の`setTimeout`の処理を行うとコールバックの入れ子が深くなり非常に見難いコードになります。

```js

setTimeout(() => {
  // do something
  setTimeout(() => {
    // do something
    setTimeout(() => {
      // do something
    }, 3000)
  }, 2000)
}, 500)


// ↓↓名前付き関数にして見やすくする
const timer = (delay, callback) => {
  setTimeout(() => {
    callback()
  }, delay)
}

timer(500, function() {
  // do something
  timer(2000, function() {
    // do something
    timer(3000, function() {
      // do something
    })
  })
})

```

コールバックが深くなること複雑になっていく印象を受けます。

このコールバック地獄を解消してくれるのがPromiseという仕組みです。

### Promiseオブジェクト

Promiseを使うには、まずPromiseオブジェクトを生成します。  
Promiseオブジェクトは`new Promise`で生成できます。

```js

const promiseInstance = new Promise((resolve, reject) => {
  // do something
})

console.log(promiseInstance) //このpromiseInstance変数にPromiseオブジェクトが代入されています。

```

Promiseコンストラクタは、初期化時に関数を引数に取ります。
さらにその関数の引数には、`resolve`と`reject`という引数2つを取ります。

この2つの引数は、何かしらの非同期処理を行なった際にその処理が終わったことを伝えることができる関数です。
2つ関数の違いは、`resolve`が処理の成功、`reject`は失敗の場合はに呼び出します。

※成功・失敗とは、例えば非同期通信でステータスコードが200で返ってきたこときは成功、それ以外は失敗とみなして`resolve`と`reject`を使い分けるということです。


`resolve`と`reject`は下記のように使います。

```js

const promiseInstance = new Promise((resolve, reject) => {

  if (/* 成功した場合 */) {
    resolve();
  } else {
    reject();
  } 
})

```

### thenとcatch

Promiseオブジェクトは`then`と`catch`というメソッドを持っています。
この2つのメソッドは`resolve`もしくは`reject`関数が呼び出されるのを待って実行されます。
そのため、`then`と`catch`というメソッドを使うことで非同期処理後の処理を行うことができます。


`then`と`catch`の違いは、`resolve`関数が呼ばれた時は`then`、`reject`関数が呼ばれた時は`catch`になります。

```js

const promiseInstance = new Promise((resolve, reject) => {

  const random = Math.floor(Math.random() * 2)

  if (random === 1) {
    resolve(); // 成功した場合
  } else {
    reject(); // 失敗した場合
  } 
})

promiseInstance
  .then(() => {
    console.log('called resolve')
  })
  .catch(() => {
    console.log('called reject')
  })
```

上記の例では、乱数を条件にして、その数値が`1`だった場合に`resolve`を、そうでない場合は`reject`を実行しています。

`promiseInstance`変数で、`then`と`catch`メソッドを使うと結果によって実行される処理が変わってくるのがわかると思います。


※`then`と`catch`メソッドはメソッドチェーンで書けます。


次に、`resolve`もしくは`reject`を非同期処理後に呼び出して見たいと思います。

```js

const promiseInstance = new Promise((resolve, reject) => {

  const random = Math.floor(Math.random() * 2)

  setTimeout(() => {
    if (random === 1) {
      resolve(); // 成功した場合
    } else {
      reject(); // 失敗した場合
    } 
  }, 3000)
})

promiseInstance
  .then(() => {
    console.log('called resolve')
  })
  .catch(() => {
    console.log('called reject')
  })
```

上記の例では、`setTimeout`を使って3秒間処理を待たせています。その後`resolve`もしくは`reject`が呼ばれます。


`then`や`catch`メソッドはその処理を待ってくれているので、`console.log`の表示はすぐに出ず、3秒後に表示されるようになります。


### thenやcatchで値を受け取る

`resolve`もしくは`reject`に引数を渡すことで`then`や`catch`でその値を受け取ることができます。

```js

const promiseInstance = new Promise((resolve, reject) => {

  const random = Math.floor(Math.random() * 2)

  setTimeout(() => {
    if (random === 1) {
      resolve('called resolve'); // 成功した場合
    } else {
      reject('called reject'); // 失敗した場合
    } 
  }, 3000)
})

promiseInstance
  .then((log) => {
    console.log(log)
  })
  .catch((log) => {
    console.log(log)
  })
```

上記のように書くことで、`then`や`catch`の第1引数に設定された関数の引数として受け取ることができます。

非同期通信で受け取ったデータを`then`や`catch`で処理する時はこのように使うことが多いです。


### コールバック地獄をPromiseで解消してみる

非同期処理の最初の例で出したコールバック地獄をPromiseを使って解消して見ましょう。

`timer`関数の戻り値をPromiseオブジェクトにすることで入れ子ではなく、`then`や`catch`を使った処理に置き換えることができます。

```js

const timer = (delay) => {
  const promise = new Promise((resolve, reject) => {
    setTimeout(() => {
      resolve()
    }, delay)
  })

  return promise
}

timer(500)
  .then(timer(1000))
  .then(timer(2000))

```


`timer`関数はPromiseオブジェクトを返すので`then`メソッドを使えるよになります。
`timer`関数で`resolve`関数が呼ばれるまで次の処理を待つという形が作れます。


### axiosの戻り値もPromiseオブジェクト

最近の非同期通信のクライアントライブラリといえば`axios`がありますが、
`axios`の`get`や`post`メソッドの戻り値はPromiseオブジェクトになります。

```js
import axios from 'axios'

axios.get('path/to/endpoint')
  .then((res) => {
    console.log(res)
  })
  .catch((error) => {
    console.error(error)
  })
```

`then`や`catch`メソッドが使えるのは戻り値がPromiseオブジェクトだからです。
通信に成功した場合は、`then`が呼ばれ、引数でレスポンスの情報を受け取れます。
反対に、失敗した場合は`catch`が呼ばれ、同じくレスポンス情報を受け取れます。


Promiseがなかった時は、jQueryの`ajax`メソッドなどで非同期通信を行なってたりしましたが、
その時の書き方に比べだいぶ前シンプルになりました。
※[jQuery deferred](https://qiita.com/opengl-8080/items/6eba7922be168edfc439)というPromiseライクなものも後発であります。


```js

// jquery ajax
$.ajax({
    url: "http://jsrun.it/assets/E/H/Z/t/EHZt3",
    success: function (data) {
        $("#results").append(data);
        // コールバックが続くとこの中にさらに非同期処理を書く
    },
    error: function () {
        alert("読み込み失敗");
    }
});


// Promise + axios
axios.get("http://jsrun.it/assets/E/H/Z/t/EHZt3")
  .then((data) => {
    $("#results").append(data);
  })
  .catch(() => {
    alert("読み込み失敗");
  })
```

### さらに１歩先へ、 async/await

Promiseにより非同期処理のコールバック地獄問題が解消されました。
しかし、それでも長い処理になると今度はメソッドチェーンが長くなり複雑になることがあります。

そこでメソッドチェーンではなく、`async/await`を使った方法が新しく用意されました。

`async/await`は見かけ上は同期処理っぽく書けるのでコードがスッキリします。

`async/await`についてはこちら
[Promise, async, await がやっていること (Promise と async は書き換え可能か？)](https://qiita.com/kerupani129/items/2619316d6ba0ccd7be6a)

## その他

- ES module(`export/import`)[http://uehaj.hatenablog.com/entry/2015/11/07/001848](http://uehaj.hatenablog.com/entry/2015/11/07/001848)
- Class構文[https://qiita.com/niisan-tokyo/items/83582bc0646239cf6cb8](https://qiita.com/niisan-tokyo/items/83582bc0646239cf6cb8)
- `Object.entries/Object.values`[https://qiita.com/ledsun/items/953b25b60592c22811ca](https://qiita.com/ledsun/items/953b25b60592c22811ca)

# 参考文献

## ECMAScriptについて
- [ECMAScriptとJavaScriptの関係 -Code Grid -](https://app.codegrid.net/entry/ecmascript-1)
- [ES2015（ECMAScript 2015）とは何か](https://thinkit.co.jp/article/10434)

## 環境設定
- [今更のバベる。 Babel 7を試してみたメモ](https://chaika.hatenablog.com/entry/2018/11/21/150000)
- [babel-preset-envを簡単にさわってみた。](https://qiita.com/ryuone/items/13f5d450c3865709ba10)
- [【５分でなんとなく理解！】Babel入門](https://qiita.com/Shagamii/items/a87181c22ea777ee2acc)

## 仕様
- [ECMAScript 2015の新機能 -Code Grid- (一部のみ無料)](https://app.codegrid.net/series/2015-es6)
- [ES2015で始めるJavaScript入門](https://qiita.com/abcang/items/824681cb88676da4f9a8)
- [ES2015(ES6) 入門](https://qiita.com/soarflat/items/b251caf9cb59b72beb9b)
- [イマドキのJavaScriptの書き方2018](https://qiita.com/shibukawa/items/19ab5c381bbb2e09d0d9)
- [【JavaScript】スプレッド演算子の便利な使い方まとめ](https://qiita.com/Nossa/items/e6f503cbb95c8e6967f8)
- [Promise, async, await がやっていること (Promise と async は書き換え可能か？)](https://qiita.com/kerupani129/items/2619316d6ba0ccd7be6a)
- [(Babel 5における)ES6のモジュールを解説してみた](http://uehaj.hatenablog.com/entry/2015/11/07/001848)
- [ES2015新機能: JavaScriptのclassとmethod](https://qiita.com/niisan-tokyo/items/83582bc0646239cf6cb8)
- [Object.values/Object.entries](https://qiita.com/ledsun/items/953b25b60592c22811ca)
- [ES仕様(es2015〜es2018)まとめ](https://qiita.com/ozoneboy/items/9c11ac3323ca94919052)