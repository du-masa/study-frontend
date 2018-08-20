## コンポーネントとは

特定のUIを部品として扱う仕組みです。コンポーネント化することで、同じようなUIや機能を持った物を何度も書く必要がなくなります。

### コンポーネント化のメリット
- 再利用性 同じコンポーネントを様々な箇所で読み込める
- 保守性 変更が発生した際に、編集箇所を少なくできる
- 可読性 1ファイルに書かれるコード量が減り、コードの見通しがよくなる

ただし、むやみやたらにコンポートネント化するとただ、ファイル量が増え、ファイル内を行き来することが増えてしまうことになるので、
開発規模や作ろうとするコンポーネントの使用頻度を考えて、コンポーネント化すると良いでしょう。

## コンポーネントの作成

コンポーネントを作成するにはいくつか方法がありますが、
今回は`.vue`ファイルを別に作って作成しましょう。

作る対象となるUIは、submitボタンにします。

`assets/components/btn.vue` ファイルを作成します。

### vueファイル

vueファイルは、少し特殊なファイルです。
1つのファイル内でhtml、js、cssを記述できます。

htmlは`<template>`タグ、jsは`<script>`タグ、cssは`<style>`タグに書きます。

```html
<template>
<!-- ここにhtml -->
</template>

<script>
// ここにjavascript
</script>

<style>
/* ここにcss */
</style>
```

コンポーネントに紐づく機能やスタイルを同一ファイル内で管理できるようになっています。

では実際にsubmitボタンをコンポーネント化してみましょう。

### templateタグ

html記述は`template`タグに書いていきます。

```html
<template>
  <button type="submit">
    送信
  </button>
</template>
```

templateタグで注意すべきことは、templateタグ直下のhtmlタグは1つでなくてはなりません。(兄弟要素を持てない)

つまり下記のような書きかたはできません。

```html
<template>
  <button type="submit">
    送信
  </button>
  <p>送信ボタンを押してください</p>
</template>
```

上記コードを実現するには、次のようにします。

```html
<template>
  <div>
    <button type="submit">
      送信
    </button>
    <p>送信ボタンを押してください</p>
  </div>
</template>
```

そのほかは基本的なhtmlと大差ありません。

### scriptタグ

jsは`script`タグに書きます。

```html
<script>
exort default {}
</script>
```

まず、コンポーネントは基本的に別ファイル(`import * from '*'`で呼び出し)で呼び出されます。
そのためには、モジュールとしてexportする必要があります。
`export default`でそのあとに続くデータを別ファイルで呼び出せるようにしています。今回はとりあえずオブジェクトをモジュールとして吐き出しています。

現在からのオブジェクトには、今まで使ってきたdataメソッド、methodsプロパティ、computedプロパティなどを指定していきます。

#### nameプロパティ

vueにはnameプロパティというものがあります。
これは、コンポーネントに対して名前をつけるものです。
無くても動きますが、デバックで役に立ちます。
vue-devetoolsでコンポーネントをみたときに、nameプロパティの名前がつけられます。

今回は`btn`という名前にしましょう。

```html
<script>
exort default {
  name: "btn"
}
</script>
```

### styleタグ

cssは`style`タグに書きます。

基本は、cssですが属性に`lang="scss"`とするとscssとして処理してくれます。

```html
<style lang="scss">
</style>
```

また、`scoped`をつけるとcssの影響範囲が、同一ファイル内となります。

```html
<style lang="scss" scoped>
</style>
```

これで、無理にclass名で名前空間を作らなくても済みます。
ボタンのスタイルをこちらに実装しましょう。

```scss
<style lang="scss" scoped>
button {
  appearance: none;
  display: block;
  width: 280px;
  margin: auto;
  background: #4ebdd5;
  border: 1px solid #4ebdd5;
  color: #fff;
  height: 50px;
  margin-top: 50px;
  border-radius: 10px;

  &:hover {
    background: lighten(#4ebdd5, 5%);
  }

  &:disabled {
    border: 1px solid #e2e2e2;
    background: #cac9c9;
  }
}
</style>
```

これで、コンポーネントファイルの準備は一通りできました。

## コンポーネントの登録

コンポーネントを利用するには、登録する必要があります。
登録には、グローバル登録とローカル登録があります。
グローバル登録は、Vue全体で使えるようにします。
ローカル登録は利用するか箇所のみで使えるようにします。

今回は、ローカル登録で利用できるようにします。

*グローバル登録についてはこちらを確認してください。

[コンポーネントのグローバル登録](https://jp.vuejs.org/v2/guide/components-registration.html#%E3%82%B0%E3%83%AD%E3%83%BC%E3%83%90%E3%83%AB%E7%99%BB%E9%8C%B2)

### ローカル登録

form.jsにコンポーネントを登録していきます。

#### コンポーネント読み込み
まずは、コンポーネントを読み込みます。

```form.js

import './scss/style.scss';
import Vue from 'vue/dist/vue.esm.js';
import btn from './components/btn.vue';

```

コンポーネント側で、`export default`としてモジュール化しているので、`import * from '*'`の形で読み込めるようになっています。

#### componentsプロパティ

componentsプロパティを使ってコンポーネントの登録をできます。

componentsプロパティにはオブジェクトを指定します。
オブジェクトのキーには、コンポーネントを実際にhtmlで使用するときの要素名、値には使用するコンポーネント(今回だと先ほど読み込んだbtnコンポーネント)を指定します。

```form.js
  components: {
    'btn': btn
  }
```

今回は、`btn`という要素で、btnコンポーネントを登録しました。

#### htmlでカスタム要素を読み込む

最後にhtmlに先ほど登録したカスタム要素を書きましょう。

もともと生のhtmlでsubmitボタンを書いていた箇所を置き換えます。

```html
  <div class="form__control">
    <label for="" class="form__label">
      問い合わせ内容<span>必須</span>
    </label>
    <textarea v-model="content" name="content" class="form__input"></textarea>
    <p
      :class="{'form__count--overlimit': contentCount < 0}"
      class="form__count">あと<strong>{{contentCount}}</strong>文字</p>
  </div>
  <btn></btn>
</form>
```

`<btn></btn>`というカスタム要素に変更しました。
ブラウザで確認すると問題なくボタンが表示されていると思います。

## disaled状態を伝える

formが送信できない状態の時に、ボタンをdisabledにするようにしていました。
しかし、ボタンをコンポーネント化してしまい、コンポーネント側では今、formが送信できる状態なのか、そうでないかを知ることができなくなってしまいました。(判断するためのデータを持っていないため)

そこでフォーム側からボタンコンポーネントにデータの状態を伝えてあげる必要があります。

### 親からデータを受け取るpropsプロパティ

コンポーネントは入れ子にして読み込んでいきます。
読み込んだ側を親(親コンポーネント)、読み込まれた側を子(子コンポーネント)と呼びます。

子コンポーネントが親コンポーネントからデータを受け取るには`propsプロパティ`を使用します。

今回は、ボタンコンポーネント側で、親であるフォームが送信可能かどうかを示す値を受け取るようにしましょう。

```html
<script>
exort default {
  name: "btn"
  props: {
    isdisabled: Boolean
  }
}
</script>
```

propsプロパティはオブジェクトで指定します。キーは、受け取る値の名前になります。値は、受け取る値の型を指定します。今回はture or flaseで受け取りたいとの`Boolean`とします。

propsで受け取った値は、親の値が変わると自動で変わります。

また、propsの値は、readOnlyで子コンポーネントで再代入することはできません。propsの値を変更するような処理をする場合は、一旦自らのdataプロパティの値に代入して使う必要があります。

```html
<script>
exort default {
  name: "btn"
  data(): {
    return {
      localIsDisabled: flase
    }
  }
  props: {
    isdisabled: Boolean
  },
  methods: {
    updateDisabled1() {
      this.isdisabled = true // これはできない
    },
    updateDisabled2() {
      this.localIsDisabled = this.isdisabled
      this.localIsDisabled = true // これは可能
    }
  }
}
</script>
```

### propsの値を使ってdisabledを制御

propsで受け取った値は、dataプロパティの値同様、htmlやjs内で参照できます。

今回受け取る`isdisabled`を使ってdisabledの制御をしましょう。

```html
<template>
  <button type="submit" v-bind:disabled="isdisabled">
    送信
  </button>
</template>
```

`v-bind:disabled="isdisabled"`とすることで、`isdisabled`がtureになれば、disabledに。flaseになれば、submitボタンの押下が可能になります。

### 親からデータを渡す

親から子のpropsにデータを渡すには、v-bindディレクティブを使います。

v-bindの引数には、子のpropsに指定した名前を指定します。属性値は、フォームが送信可能かどうかを判定している`isInvalid`を使います。

```html
<btn v-bind:isdisabled="isInvalid"></btn>
```

これで、親から子にデータを伝えることができます。

### 子からデータの変更を伝える

親から子にデータを伝えるにはpropsを使いました。

反対に子の値が変更されたことを親に伝えるには`$emit`というメソッドを利用します。
`$emit`メソッドの第1引数には、親側で登録された独自のイベント名を指定します。
第2引数以降には親に渡したいデータを指定します。

これで親側に登録された独自イベントを着火できます。

一方親側では、`v-on:独自イベント名` をコンポーネントのタグに指定しイベントを受け取ります。

```html
<!-- 子コンポーネント -->
<template>
  <button v-on:click="updateChildData">button</button>
</template>

<script>
export default {
  name: 'childComponent',
  data: function() {
    return {
      childData1: false
    }
  }
  methods: {
    updateChildData: function() {
      this.childData1 = !this.childData1
      this.$emit('catch-update-child-data', this.childData1)
    }
  }
}
</script>

```

```html
<!-- 親コンポーネント -->
<template>
  <child-componet v-on:catch-update-child-data="updateParentData"></child-componet>
</template>

<script>
import childComponent from 'childComponent'
export default {
  data: function() {
    return {
      parentData1: false
    }
  }
  methods: {
    updateParentData: function(childData1) {
      console.log('子コンポーネントの値が ' + childData1 + ' に変更されました')
      this.parentData1 = childData1
    }
  },
  components: {
    'child-componet': childComponent
  }
}
</script>

```

上記の例ではまず、
子コンポーネントにあるボタンがクリックされたら`updateChildData`メソッドが実行されます。`updateChildData`メソッド内ではデータの更新と共に`$emit`メソッドを呼んでいます。第1引数は`'catch-update-child-data'`、第2引数はデータを更新した`childData1`です。

これを親側では受け取れるようにしています。コンポーネントのタグにディレクティブで`v-on:catch-update-child-data`を指定しています。`catch-update-child-data`は子供側の`$emit`メソッドの第1引数で指定した名前と同じにします。

そして、ディレクティブの属性値には`catch-update-child-data`イベントを受け取った後に実行したい処理を指定します。今回は`updateParentData`メソッドを指定しています。
この場合、`updateParentData`メソッドの引数には`$emit`メソッドの第2引数以降に指定したデータが入ります。

これで、子から親へのデータの伝播を実現できました。
