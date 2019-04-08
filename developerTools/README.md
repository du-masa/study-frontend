# デベロッパーツールを使いこなす

デベロッパーツールを使うことでコードのデバックやパフォーマンスチューニングをおこなうことができます。　

各ブラウザごとにデベロッパーツールは用意されていますが、今回扱うのはGoogle Chrome Developer Toolsdです。

# 起動

デベロッパーツールを使うにはまず起動する必要があります。

検証を行いたいwebページを開いた状態で右クリックをして、「検証」を選ぶと起動しますが、
ショートカットを覚えると便利です。

ショートカットは３種類あります。

- `commond + option + c`: 通常起動
- `commond + option + j`: 起動/停止
- `commond + option + i`: 前回開いたパネルを保持したまま、起動/停止


# パネル

デベロッパーツールは機能ごとにパネルで分割されています。
2019年4月現在、９つのパネルがあります。

## Elementsパネル

HTMLの構造や要素のCSSを確認でき、リアルタイムで変更可能で試し実装やレイアウト崩れなどの検証が行えます。

### Elementsパネルの構成

メインエリアにはDOMツリー表示され、その右もしくは下には要素に当てられたスタイルなどを表示するサイドバーがあります。

### Elementsパネルの機能

#### 要素の選択

Elementsパネルで検証を行うときは見たいHTML要素を選択する必要があります。

選択には左上の矢印アイコンをクリックするか、ショートカット`commond + option + c`を入力しましょう。

その状態で表示しているwebページから直接要素を選択できます。

#### 要素を編集する

選択した要素は属性値を追加、編集を行ったり、要素の削除や非表示など行えます。


##### 属性編集

選択した要素の属性をダブルクリックすると文字を入力できるようになり、編集ができます。

また、右クリックから`Add Attribute`や`Edit Attribute`を選んでも属性を操作できます。


##### 要素の削除、非表示

選択した要素はdeleteキーもしくは右クリックの`Delete Element`で削除できます。

非表示は右クリック`Hide Element`を選択で行えます。有効にすると要素に`visivility:hidden`のスタイルが付与されます。

#### 要素の入れ替え

DOMツリーで要素の順番を入れ替えるには、選択した要素を移動させたいところまでドラッグするだけで出来ます。

#### 擬似クラスのついた状態に固定

擬似クラスが付いた状態の検証は状態固定をして行います。

右クリックで`Force State`を選択します。その中には`:hover`や`:active`があります。これらを有効にすることで擬似クラスが付いた状態の検証が行えます。

サイドバーにある、`Styles`ペインの`:hov`ボタンでも同じことができます。

#### Stylesペインでスタイルを編集

要素を選択するとその要素に当たっているスタイルがStylesペインに表示されます。
表示されているプロパティの値を変更したり、新しくプロパティを追加したり、無効にしたり出来ます。

#### セレクタを追加

Stylesペインのプラス記号をクリックすると選択中の要素のセレクタでCSSルールが追加されます。

#### classを追加

Stylesペインの`.cls`をクリックすると、選択中の要素にクラスを追加できます。inputタグに任意のクラス名を入力すると、要素に追加されます。
また、inputタグの下にチェックボックスが表示されている場合があります。これは選択中の要素に指定されているクラス名になります。チェックボックスのチェックをoffにすると一時的にそのクラス名を要素から外すことができます。

#### ビューへのスクロール

要素を選択しているときに、webページをスクロールして要素が画面から外れてしまうときがあります。デベロッパーツールで要素があるところにスクロール位置を移動することできます。
選択された要素で右クリックをして、`Scroll into view`を押すとスクロール位置を移動してくれます。

#### DOM ブレイクポイント

DOMの変化に応じてブレイクポイントを設定できます。

ブレイクポイントを設定するには右クリックして`Break on`を選択します。

DOMに設定できるブレイクポイントは３つです。

- Subtree Modified
- Attribute Modified
- Node Removed

※コード出典[Tools for Web Developers DOM の編集](https://developers.google.com/web/tools/chrome-devtools/inspect-styles/edit-dom?hl=ja#dom_4)

##### Subtree Modified

サブツリーの変更を検知します。サブツリーの変更とは、ブレイクポイントを設定した要素のこ要素に何らかの変更があった場合に処理を止めることができます。

例えば、下記のような子要素を追加するコードを実行すると処理が途中で止まります。

```html
<div id="main-content">
    aaa
</div>
<button id="addSubtreeButton">add subtree</button>
```

```js
const button = document.getElementById("addSubtreeButton");

button.addEventListener('click', function() {
    const element = document.getElementById('main-content');
    //modify the element's subtree.
    const mySpan = document.createElement('span');
    element.appendChild( mySpan );
})
```

##### Attribute Modified

選択した要素の属性に変更があった際に処理を止めることができます。

例えば、下記のような要素にクラスを追加するコードで処理が止まります。


```html
<div id="main-content">
    aaa
</div>
<button id="addClassButton">add class</button>
```

```js
const button = document.getElementById("addClassButton");

button.addEventListener('click', function() {
    var element = document.getElementById('main-content');
    // class attribute of element has been modified.
    element.className = 'active';
})
```

##### Node Removed

選択した要素自体を削除した時に処理を止めることができます。

以下のコードで処理が止まります。


```html
<div id="main-content">
    aaa
</div>
<button id="removeElement">remove element</button>
```

```js
const button = document.getElementById("removeElement");

button.addEventListener('click', function() {
    document.getElementById('main-content').remove();
})
```

※`display:none`で要素を隠している場合は、発動されません。

##### ブレイクポイントの確認

DOMに設定されているブレイクポイントを確認するには、サイドバーの`DOM Breakpoints`ペインで確認します。

ここには、選択された要素だけでなく現在開いているWebページ全体で設定されているブレイクポイントが表示されます。

ここで行えることは下記になります。

- 要素にカーソルを当てることでページ上での要素の対応する位置が示されます
- 要素をクリックすることでElementsパネルで選択状態になります。
- チェックボックスをオン/オフにすることでブレイクポイントの無効有効を切り替えられます。

#### 要素のイベントリスナーを確認

サイドバーの`Event Listeners`ペインで選択中の要素に関連つけられているイベントリスナーを確認することができます。

例えば、選択中の要素にクリックイベントが関連つけられていたら`Event Listeners`ペインを見ると`click`と表示されます。さらにそれをクリックすると詳細が確認できます。

イベントのコールバック関数を確認するには`hundler`を選択して右クリック、`Show function Definition`を選ぶことでできます。

##### Ancestors

`Event Listeners`ペインの上部にある`Ancestors`チェックボックスをオンにすると、祖先要素のイベントまで確認することができます。

##### Framework listeners

イベントの詳細を見ると、右側にどこで関数が定義されているか表示されます。
しかし、ライブラリ(例えばjQuery)でカスタムされたイベントを使うと、ライブラリ内のコードを参照してしまうため、デバックが難しくなります。

しかし、`Event Listeners`ペインの上部にある`Ancestors`チェックボックスをオンにすると、デベロッパーツールがその問題を解決してくれて、実際にユーザーが定義した関数の位置を表示してくれます。

## Consoleパネル

Consoleパネルの機能は大きく分けて２つです。１つはコードのログ出力(`console.log`など)、もう一つは対話式でのJavaScriptの実行です。

### consoleオブジェクト

JavaScriptのコード上でログを出力するには　`console`オブジェクトを使うことで実現できます。`console`オブジェクトは[複数の便利なメソッド](https://developer.mozilla.org/ja/docs/Web/API/console)がありデバックの用途に応じて使い分けられます。ここではいくつか便利なメソッドをご紹介します。

#### console.log

言わずと知れた、代表的なログ表示のメソッドです。引数に変数を渡して中身を見たり、指定した処理が実行されたかどうかのか確認等で使います。

```js
console.log("this is console.log")
```

#### console.error console.warn

基本的な使い方は`console.log`と同じですが、ログを目立たせたい時に使います。
例外処理などでエラーが起きた時に`console.error`などでログを出すことで処理のミスを発見しやすくします。
ライブラリなどでもライブラリ利用者が間違えた実装を行った時などに、エラーとして伝えるためにこちらのログ表示が使われたりします。

```js
console.warn("this is console.warn")
console.error("this is console.error")
```


#### console.group console.groupEnd

`console.log`などのログ出力をグループ化して表示することができます。
ログが大量にあると確認したい項目のまとまりがわかりにくくなります。そんな時にグループでまとめてしまうと便利です。

`console.group`メソッドで始まり、`console.groupEnd`メソッドで終わりを指定します。


```js
console.group("this is console.group1")
    console.log("this is group1 log1")
    console.log("this is group1 log2")
console.groupEnd()

console.group("this is console.group2")
    console.error("this is group2 log1")
    console.log("this is group2 log2")
console.groupEnd()
```

#### console.table

コンロール上にオブジェクトの内容を表形式にして表示します。オブジェクトを格納した配列の中身を確認する時などに見やすくなるので便利です。

```js
const list = [
    {
        id:1,
        value: "A"
    },
    {
        id:2,
        value: "B"
    },
    {
        id:3,
        value: "C"
    }
    {
        id:4,
        value: "D"
    }
]
console.table(list)
```

#### console.time console.timeEnd

処理計測に役立ちます。
計測開始位置に`console.time`メソッド、終了位置に`console.timeEnd`メソッドを使用します。
お互いの引数には任意の同じ文字列を渡す必要性があります。


```js
console.time('logTimer')
// do something...
console.timeEnd('logTimer')
```

#### その他のメソッドと注意事項

その他のメソッドについては[MDN web docs](https://developer.mozilla.org/ja/docs/Web/API/console#Methods)でご確認ください。
注意事項としては、全てのメソッドが標準化されているわけではないので、ブラウザによって差異があったり、今後使えなくなる恐れがあります。

