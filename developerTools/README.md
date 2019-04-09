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

### consoleパネルで使えるAPI

consoleパネルでは、試しに動かしてみたいJavaScriptのコードを実行できます。

その際に使える便利なAPI用意されています。

#### $_

最後に実行して表示された結果を保持している変数です。
もしくは最後に選択したDOM要素が入ります。

```js

1 + 1
// => 2

$_
// => 2


3 + 3
// => 6

$_
// => 6
```


#### $0 - $4

過去に選択されたDOM要素が格納されてます。
$0から$4までの変数があり、4が一番古い履歴です。

#### $(selector, [startSelector])

`document.querySelector`のエイリアスになります。

第２引数で検索を開始するDOM要素を指定することができます。

#### $$(selector, [startSelector])

`document.querySelectorAll`のエイリアスになります。

第２引数で検索を開始するDOM要素を指定することができます。

#### clear()

consoleのログ表示をリセットします。

#### dir()

DOM要素をオブジェクトとして表示してくれます。

#### その他

その他にも便利なAPIが用意されているので公式サイトをご参照ください。
[Console Utilities API Reference](https://developers.google.com/web/tools/chrome-devtools/console/utilities?hl=ja)

### ログを保持

リロードしたり、他のページに遷移した際にログは消えてしまいます。
ページをまたいでのデバックを行い時はログを残すように設定できます。
consoleパネルに表示されている`Preserve log`のチェックを入れることでログを引き継ぐことができます。

### XMLHttpResuestのログを表示する

Ajax通信されたことをログに残すことができます。
consoleパネルに表示されている`Log XMLHttpResuests`のチェックを入れることでログが表示されます。

## Sourcesパネル

現在開いているページでダウンロードされたHTML、CSS、JavaScript、画像などのソースが一覧で確認できます。

JSのブレイクポイントを使ったデバックもSourcesパネルでできます。


### Sourcesパネルの構成

Sourcesパネルは主に３つエリアで構成されています。

1. ソース一覧
2. 一覧から選択したソースの表示
3. ブレイクポイントや変数の内容の確認

### ソースの変更

CSSやJSはコードを修正することでその場で変更を確認できます。

CSSは変更を加えたらリアルタイムで反映されます。
JSは`commond + s`で保存することで反映されます。


また、ソースを選択した状態で右クリックで`Local Modifications`を選択することで変更の差分を確認できます。

### ワークスペースを利用してファイルに変更を保存

ワークスペースを使うことでデベロッパーツール上で変更した内容をローカルファイルにそのまま保存することができます。

ソース一覧の`Filesystem`タブを選択して、**Add folder to workspace**を選択します。
そこでワークスペースとして保存したいフォルダを選択します。
Choromeから許可を求められて、許可すると、デベロッパーツール上フォルダが追加されます。

この状態でファイルを変更すると、ローカルファイルも同時に変更されます。

### Snippets

スニペットを使うことで、デバックで汎用的に使えるスクリプトを保存することができます。

ソース一覧の`Snippets`タブを選択して**New snippet**で追加できます。

登録したスニペットは右クリックして、`Run`ボタンで実行できます。

デバックで使える便利なスニペット集もあります。
[DevTools Snippets](https://bgrins.github.io/devtools-snippets/)

### コードの自動整形

ソースがminifyされていると中身を確認するのが難しくなります。
デベロッパーツールではボタン一つで自動整形を行ってくれます。

整形したいソースを開いて、左下部にある`{}`をクリックすることで綺麗に整形してくれます。

### ブレイクポイント

JavaScriptのコードにブレイクポイントを設定することができます。

ブレイクポイントを設定することで変数の中身や処理の流れを確認することができ、バグの検証に役立ちます。

#### コード行ブレイクポイント

特定のコード行に対してブレイクポイントを設定することができます。
JavaScriptがその行を実行するときに必ず一時停止します。


コード行ブレイクポイントの設定はSourcesパネルの実コードの行番号を押すことで設定できます。


また、コード内に`debugger`を書くことで処理が止まります。

```js
// do something
debugger;
// do something
```

#### 条件付きブレイクポイント

コード行のブレイクポイントと同じような動きをしますが、特定の条件のみ一時停止するという設定ができます。

コード行のブレイクポイントと同じようにソースの行番号を選択後、右クリックで`Edit breakpoint`を選択します。そうすると入力フォームが表示されるので、真偽値が返るように条件式を入力します。

条件が`true`になった時のみ一時停止します。


#### イベントリスナーブレイクポイント

イベント発生後のコールバック関数で処理を一時停止することができます。

ブレイクポイントを設定するにはSourcesパネルの`Event Listener Breakpoints`の選択します。中を見るとイベントのカテゴリーが表示されています。それをさらに展開すると`click`などのイベント名があります。

例えば`click`イベント発生後の関数で一時停止したい場合は`click`のチェックボックスを有効にします。


#### XHRブレイクポイント

XHRブレイクポイントはAjaxでのリクエストURLに対して任意の文字列を指定して処理を一時停止できます。
ブレイクポイントを設定するにはSourcesパネルの`XHR Breakpoints`の選択します。
`Add breakpoint`を選択して、任意の文字列を入力します。

#### DOMブレイクポイント

[こちら]()をご確認ください

#### ブレイクポイントの操作ボタン

ブレイクポイントを操作するボタンについて説明します。

① 次のブレイクポイントまで進みます。次のブレイクポイントがなかった場合は、一時停止を終了します。
② 次の行へ移動します。関数の実行があったとしてもその内部までは移動しません。
③ 次の行へ移動します。関数の実行があった場合は、その内部まで移動します。
④ 関数内の場合はその関数を終了し、関数の呼び出し元に戻ります。関数外の場合は一時停止を終了します。
⑤全てのブレイクポイントを一時的に無効にします。
⑥例外が発生したときに一時的にコードを一時停止します。


#### 関数の呼ばれた順番を確認する

関数が入れ子で呼ばれているときに、目視で追うのは面倒です。
Sourcesパネルの`Call Stack`を見ると、ブレイクポイントでの停止時点で関数がどの順番で呼ばれているかがわかります。

#### 変数の中身を確認する

停止時点での変数の中身を確認することができます。

一つは、Sourcesパネルの`Scope`で確認できます。`Scope`には、グローバル変数と現在実行中のスコープのローカル変数が表示されます。ブレイクポイントの位置を動かすことで変数の値が変化していくのがわかります。

もう一つは、`Watch`で確認できます。ここにはあらかじめ監視したい変数名を登録しておくことで、実行位置が進むにつれてどのように値が変化していくかを確認できます。

### ElementsパネルのstylesペインからCSSソースにルールを追加する

Elementsパネルのstylesペインのプラスマークを長押しすると、`inspect-stylesheet`の他に現在ダウンロードされたCSSファイルが表示される。CSSファイルを選択すると、stylesペインに選択中の要素のセレクタが自動的に設定されたCSSルールが追加されます。
書き先のソースは先ほど選んだCSSファイルになります。

これにより、SourcesパネルからわざわざCSSファイルを変更しなくて済みます。
`inspect-stylesheet`に新規追加するとスタイルの優先順位が高くなってしまうので、優先順位を意識したいときなどに便利かもしれません。

# ドロワー

デベロッパーツール下部に表示される付属機能のエリアです。デベロッパーツールを開いた状態で`esc`キーを押すか右上にある<img src="/image/developerTools/menu.png" width="20">ボタンから`Show console drawer`を選択することで開かれます。

ドロワーにタブを追加するにはドロワー左側にある<img src="/image/developerTools/menu.png" width="20">ボタンを選択して、使いたいタブを追加します。

## 使ってないコードを検出する　Coverage

ページ内で使われていないCSSやJSを見つけることができます。

ページを開いた状態で、<img src="/image/developerTools/start.png" width="20">を押します。
そうすると、ページ内で読み込まれているファイル一覧が表示されて、その中で使用されている部分とされてない部分を表示してくれます。

一覧の`URL`からファイルを選択するとSourceパネルが開かれ、行番号の左側に緑と赤のラインが表示されます。緑は使用している部分、赤は使用してない部分を示します。

この結果を参考にコードのスリム化を実現できます。

しかし、赤く表示されたからといって全てを闇雲に消していいわけではありません。
下記に注意して不要なコードを特定していく必要があります。

- レスポンシブデザインで検証を行った画面サイズでのみ不要なコードと判断されてないか
- 他ページとの共通ファイルの場合、他のページで使用されていないか
- イベントハンドラーなど、イベントが実行されてないから不要なコードと判断されていないか

## アニメーションの検証を行う Animations

アニメーションを自動で検出し、アニメーションのスピードやタイミング、長さなどを調査して理想の動きに変更できます。


例えば、タブ切り替えにアニメーションをつけていたとします。
タブを切り替えるとアニメーションが自動で検出されます。

複数のアニメーションが同時に動く場合は、デベロッパーツール側で自動的にグループ化されて調査しやすいようになります。

検出されたアニメーションを選択すると、アニメーションの詳細が表示されます。
詳細一覧の左側には、アニメーションが発生しているDOM要素、右側にはプロパティ名やアニメーションの動き、タイミング、長さが表示されます。

一覧の上にある<img src="/image/developerTools/play.png" width="20">を押すとアニメーションがスタートします。

### アニメーションの速度を調整

<img src="/image/developerTools/animationspeed.png" width="120">でアニメーションのスピードを調整することができます。

### 要素ごとのアニメーションの長さやタイミング調整

詳細一覧の右側にあるアニメーションをドラッグしたり、円の位置をずらしたりすることで長さやアニメーション開始のタイミングを調整できます。

ここで変更した内容は、CSSに反映されます。
 
# 参考文献
- [Tools for Web Developers](https://developers.google.com/web/tools/chrome-devtools/?hl=ja)
- [Chrome DevToolsを使いこなそう！ Web開発に必須なブラウザ開発ツールによるデバッグの基本](https://employment.en-japan.com/engineerhub/entry/2017/05/30/110000#Securityパネル)
- [Web開発でよく使う、特に使えるChromeデベロッパー・ツールの機能](https://www.buildinsider.net/web/chromedevtools/01)
- [ブレークポイントを使ったJavaScriptデバッグを整理してみた【再入門】](https://qiita.com/nekoneko-wanwan/items/02aed17773833c3ad3b2)
- [MDN web docs 開発ツール](https://developer.mozilla.org/ja/docs/Tools)