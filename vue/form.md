## 内容
vue.jsを使って簡単な入力フォームを作成します。
vue.jsを利用することで、フロントで行う処理(エラー表示の制御や値の加工など)
を容易にします。

## リポジトリ
https://github.com/du-masa/vue-form/tree/master/assets

### 今回新たに使うvue.jsの機能
- v-modelディレクティブ
- computedプロパティ

## 便利ツール
- [vue.js devtools](https://chrome.google.com/webstore/detail/vuejs-devtools/nhdogjmejiglipccpnnnanhbledajbpd)

consoleを開かなくてもdataの中身など確認することが可能です。

## v-modelを使ったフォーム値の制御

inputタグやtextareaタグの属性にv-modelにdataプロパティの値を指定することで、データの双方向バインディングを実現します。双方向バインディングはinputタグに入力された値は自動的にv-modelに指定した値に代入されます。また、javascript内でv-modelに指定された値に別の値を代入し場合は、そのinputのvalue値が切り替わります。


・v-modelサンプル
```html
<div id="form">
  <input v-model="message" placeholder="edit me">
  <p>Message is: {{ message }}</p>
</div>

<script>
  new Vue({
    el: "#el"
    data: function() {
      return {
        message: ''
      }
    }
  })
</script>
```

### 「名前」を入力するinputタグにデータをバインディング

「名前」を入力するinputタグにv-model属性を値をバインディングしましょう。

```html
<!-- index.html -->
<div class="form__control">
  <label for="" class="form__label">
    名前
  </label>
  <input
    class="form__input"
    v-model="name"
    type="text"
    name="name"
  >
</div>
```

```js
// form.js
new Vue({
    el: '#form',
    data: function() {
      return {
        name: '',
        content: ''
      }
    }
  }
)
```

html側では、inputタグに `v-model="name"` を指定して、dataプロパティには `name: ''` を指定しています。
* 初期値を指定する場合は、`name: '初期値'`のようにあらかじめ値を指定します。そうするとinputタグに最初から値が入力された状態になります。

inputタグに何かしら値を入力すると`name`の値も変化します。vue.js devtoolsを使って確認してみましょう。

### ワーク1 「問い合わせ内容」を入力するtextareタグにもデータをバインディング

「問い合わせ内容」を入力するtextareタグにもデータをバインディングしてみましょう。

### セレクトボックスにデータをバインディング

セレクトボックスもv-modelを使うことで双方向バインディングが可能です。
性別を選ぶセレクトボックスを編集してみましょう。

```html
<!-- index.html -->
<div class="form__control">
  <label for="" class="form__label">
    性別
  </label>
  <select name="gender" class="form__input" v-model="gender">
    <option value="male">男性</option>
    <option value="female">女性</option>
    <option value="other">その他</option>
  </select>
</div>
```

```js
// form.js
new Vue({
    el: '#form',
    data: function() {
      return {
        name: '',
        content: '',
        gender: 'male'
      }
    }
  }
)
```

html側では、selectタグに `v-model="gender"` を指定して、dataプロパティには `gender: 'male'` を指定しています。
selectボックスは初期選択にしたいoptionタグのvalue属性値をgenderの値に指定してあげれば実現できます。

### ラジオボタンにデータをバインディング

ラジオボタンもv-modelを使ってデータをバインディングしましょう。

```html
<!-- index.html -->
<div class="form__control">
  <label for="" class="form__label">
    問い合わせの種別
  </label>
  <div class="form__radioList">
    <div class="form__radioItem">
      <input name="kind" id="kind1" value="kind1" v-model="kind" type="radio">
      <label for="kind1">問い合わせ1</label>
    </div>
    <div class="form__radioItem">
      <input name="kind" id="kind2" value="kind2" v-model="kind" type="radio">
      <label for="kind2">問い合わせ2</label>
    </div>
    <div class="form__radioItem">
      <input name="kind" id="kind3" value="kind3" v-model="kind" type="radio">
      <label for="kind3">問い合わせ3</label>
    </div>
  </div>
</div>
```

```js
// form.js
new Vue({
    el: '#form',
    data: function() {
      return {
        name: '',
        content: '',
        gender: 'male',
        kind: 'kind1'
      }
    }
  }
)
```

html側では、input[type="radio"]タグ全てに `v-model="kind"` を指定して、dataプロパティには `kind: 'kind1'` を指定しています。
ラジオボタンで初期選択にしたいinputタグのvalue属性をkindの値に指定してあげれば実現できます。

### バインディングさせることの意味

データをバインディングさせることで、ユーザーの入力・選択に応じて値が自動的に更新されます。入力された値に応じて何か処理したい場合に便利です。(jqueryのようにvalメソッドで毎回取得する処理が必要ない)

## 「お問い合わせ内容」の入力可能文字数を表す処理を作る

「お問い合わせ内容」をあと何文字入力できるかをユーザーに伝えたいと思います。
上限の文字数を用意し、それと入力された値を比べた値を使いたいと思います。

### 算出プロパティ computed

vue.jsでは、html内にjavascriptの式を埋め込むことができます。

```html
<div id="example">
  <div>
    {{1 + 2}}
    <!-- 3 が表示される -->
  </div>
  <div>
    {{ message.split('').reverse().join('') }}
    <!-- edcba と表示される -->
  </div>
</div>

<script>
  new Vue({
    el: "#example"
    data: function() {
      return {
        message: 'abcde'
      }
    }
  })
</script>
```

上記のように書くことは可能です。しかし、html内にjavascriptの処理を書くことでコードは肥大化し、メンテナンス性が下がります。

そんな時に `computed` プロパティを使います。

computedプロパティはmethodsプロパティのように、複数のメソッドを指定できます。各メソッドは処理を行なった結果を戻り値として返すことで、そのメソッド名をそのままhtmlや他のメソッドで使用できます。

先ほどの例をcomputedプロパティを使ってみましょう。

```html
<div id="example">
  <div>
    {{calcNum}}
    <!-- 3 が表示される -->
  </div>
  <div>
    {{ reverseMessage }}
    <!-- edcba と表示される -->
  </div>
</div>

<script>
  new Vue({
    el: "#example"
    data: function() {
      return {
        message: 'abcde'
      }
    },
    computed: {
      calcNum: function() {
        return 1 + 2
      },
      reverseMessage: function() {
        return this.message.split.reverse().join('')
      }
    }
  })
</script>
```
computedプロパティには `calcNum` と `reverseMessage` メソッドを設定しています。その中身はそれぞれ処理した値を戻り値として返しています。
html側では、そのメソッド名をそのまま指定していて、実行されると戻り値の値が表示されます。

### computedプロパティを使って文字数をカウントする

今回は、残り入力可能な文字数を表示します。
そのためには、上限の文字数から現在入力されている文字数を引くことで値が得られます。

```html
<!-- index.html -->
<div class="form__control">
  <label for="" class="form__label">
    問い合わせ内容<span>必須</span>
  </label>
  <textarea v-model="content.value" name="content" class="form__input" @input="checkContentValue">
  </textarea>
  <p class="form__count">あと<strong>{{contentCount}}</strong>文字</p>
</div>
```

```js
// form.js
new Vue({
    el: '#form',
    data: function() {
      return {
        name: '',
        gender: 'male',
        content: '',
        kind: 'kind1',
        contentLimitNum: 300
      }
    },
    computed: {
      contentCount() {
        return this.contentLimitNum - this.content.length
      },
    }
  }
)
```

jsにcomputedプロパティで `contentCount` メソッドを定義しています。そのメソッド内で上限の文字数を表す `contentLimitNum` から現在入力されている文字数 `content.value.length` を引いてその値を戻り値にしています。
html側では、 `contentCount` を変数のようにそのまま埋め込んで使用します。「お問い合わせ内容」
のtextboxに文字が入力されるたびに計算が行われて、残り文字数が変化するのがわかると思います。

### ワーク2 残り文字数が0未満になったら、文字色を変更させよう

残り文字数が0未満になったことをユーザーに知らせるために、文字の色を変えるようプログラムを
変更してみましょう。
文字色は、`#ff3434` にしましょう

### ワーク3 submitボタンの制御をしてみよう

入力値に応じてsubmitボタンの制御を行いたいと思います。今回はフォームの送信条件に満たさない場合は、submitボタンを押せないようにして、条件に満たして初めて押せるようにします。

#### フォーム送信可能な条件
- 必須項目が全て入力されていること
- 「お問い合わせ内容」の文字数が上限以内に収まっていること

#### submitボタンの表現方法
- 条件に満たしていないときは、ボタンの色を `#cac9c9` にして、クリックしてもフォームの内容が送信されないようにする
- 条件に満たした場合は、ボタンの色を `#4ebdd5` にして、クリックしてフォームの内容を送信できるようにする

*ヒント: 送信の制御は、disabled属性を使うと実装しやすいです。

#### 更なる発展

余裕がある方は、下記にも挑戦してみましょう

- 「名前」「お問い合わせ内容」に入力された値が、空っぽだったら「必須項目です」というテキストのアラートを出すようにしましょう(ただし、初期表示時は出さない)
- セレクトボックスを使って生年月日を入力するエリアを追加しましょう。
  - セレクトボックスは、「年」「月」「日」に分けて用意しましょう。(3つのセレクトボックスを使用する)
  - 「年」の選択範囲は1940年から2018年とします。
  - 実際にフォーム送信する際は、「年-月-日」にフォーマットした値を送るようにしましょう。(type=hidden"を使う)
  - 不正な日付(閏年でない2/29や4/30など)や未来の日付(2018/12/30)などが入力されたらエラーを出すようにしましょう。
- submitボタンの制御に、「生年月日の入力がエラー時は送信できないようにする」という条件を追加しましょう
