# Storybook

Storybookは、スタイルガイド・UIのカタログ集のようなものを構築できます。
VueやReactなどのフレームワークに対応しており、コンポーネント単体でどのようなUIになっているかの確認ができます。

エンジニア間だけでなく、デザイナーやディレクターとのコミュニケーションのツールとしても活用できます。

また、コンポーネント単体のテストとしても使用できます。

公式サイト: [Storybook](https://storybook.js.org/)

## 活用するメリット

- 現在用意されているコンポーネント(UI)が一覧で確認できる
- コンポーネント単位での開発がしやすい
- データを書き換えた時のUIテストができる(簡易的なユニットテスト)
- エンジニア・デザイナー・ディレクター間のコミュニケーションツール
- 仕様書的な役割も担える

## 環境設定

今回はVue.js(Nuxt.js)向けの環境を作っていきます。

Nuxt.jsの初期設定は完了していることを前提とします。

### インストール

Storybookはv5.2を使います。

```bash
$ npm install @storybook/vue@5.2.1 --save-dev
```

Storybookはフレームワークごとにインストール対象が分かれています。今回はVue.jsなので `@storybook/vue` をインストールします。


Vue関連の依存モジュールをインストールします。
今回はすでにNuxt.jsでインストールしているものもあるので、`babel-preset-vue` のみインストールします。

```bash
$ npm install babel-preset-vue --save-dev
```

必要に応じてVue関連の依存モジュールをインストールしてください。
https://storybook.js.org/docs/guides/guide-vue/#add-peer-dependencies


### ファイル設定

まずは、npm scriptにStorybookコマンドを設定します。

```package.json
// package.json
  "scripts": {
    ...do something,
    "storybook": "start-storybook"
  },
```

Storybookのconfigファイルを設定します。
`.storybook`ディレクトリを作り、その中に`config.js`を用意します。

```bash
$ touch .storybook/config.js
```

```js
// .storybook/config.js
import { configure } from '@storybook/vue';

configure(require.context('../components', true, /\.stories\.js$/), module);
```

Storybookの`configure`関数にStorybookの対象となるファイルを指定します。上記の設定では、`components`　ディレクトリの`.stories.js`が対象になります。


これで設定は終わりです。
Storybookの画面をブラウザで確認してみましょう。

```bash
$ npm run storybook
```

<img src="/image/storybook/storybook.png" width="700">

## Storyを作る

それでは、Storyを追加していきましょう。(Storybookに追加するコンポーネントをStoryというらしい？)

`components/Button.vue`というコンポーネントがあるのでこちらのStoryを作ってみます。

まずは、Storyのファイルを作ります。

```
$ touch ./components/Button.stories.js
```

次にVue.js本体とコンポーネントを読み込みます

```js
// Button.stories.js
import Vue from 'vue';
import ButtonComponent from './Button';
```

### グループわけする

Storyはグループ分けできます。

```js
// Button.stories.js
import Vue from 'vue';
import ButtonComponent from './Button';

export default { title: 'Button' }; // Buttonという名のグループを指定する
```

`export default { title: 'グループ名' };` でStoryを登録するグループを指定できます。

さらに細かくグループ分けすることも可能です。

```js
// Button.stories.js
import Vue from 'vue';
import ButtonComponent from './Button';

export default { title: 'Atoms|Button' }; // Atoms/Buttonという名のグループを指定する
```

※他のコンポーネントを同じグループに登録するには、同一ファイルに記述する必要性があります。

### コンポーネントをStorybookに表示する

では、Storybookでコンポーネントを表示しましょう。

```js
// Button.stories.js
import Vue from 'vue';
import ButtonComponent from './Button';

export default { title: 'Atoms|Button' }; // Atoms/Buttonという名のグループを指定する

export const BasicButton = () => ({
  components: { ButtonComponent },
  template: '<ButtonComponent text="This is Basic Button" />',
});
```

関数を定義し、その戻り値としてVueコンポーネントを定義します。その関数名はStoryの名前(上記の例では`BasicButton`)になります。この関数を`export`することでStoryに登録されます。

<img src="/image/storybook/buttonStory.png" width="700">

### 状態変化を確認する

今回登録したButtonコンポーネントは、クリックする度に色が変わります。このように状態に応じたコンポーネントの変化をStorybookで確認できます。

<img src="/image/storybook/buttonState.gif" width="700">

### Propsの変化に応じたUIの変化を確認する

前途の例では、コンポーネント自身のStateを変化された例でした。
次は、親から渡ってきた`Props`に応じたUIの変化を確認します。

下記のようなボタンを用意しました。
ボタンをクリックすると何らかの処理が走ってローディング状態になります。ローディング状態は親で管理しているので、親から渡ってくる`Props`に応じてUIが変化します。


<img src="/image/storybook/loadingButton.gif" width="700">


こちらのStoryを作っていきます。

まずは、`LoadingButton` のコンポーネントを追加します。

```js
// Button.stories.js
import Vue from 'vue';
import ButtonComponent from './Button';

// 新しく追加
import LoadingButtonComponent from './LoadingButton';

```

`LoadingButton`用のStoryを追加します。

```js
// Button.stories.js

export const LoadingButton = () => ({
  components: { LoadingButtonComponent },
  template: `<LoadingButtonComponent
    text="This is Loading Button"
    :isLoading="isLoading"
    :handle-click-button="handleClickLoadingButton"
  />`,
  data() {
    return {
      isLoading: false,
    }
  },
  methods: {
    handleClickLoadingButton() {
      this.isLoading = true;
      setTimeout(() => {
        this.isLoading = false;
      }, 2000);
    }
  }
});
```

最初に作った`BasicButton`とは違って、親のStateをPropsとして渡しています。そして、ボタンがクリックされたことを通知を受けて`isLoading`Stateを更新ししています。

これにより、Storybook上で親からのPropsに応じたUIの確認ができます。

<img src="/image/storybook/loadingButton2.gif" width="700">

## Vuexとの連動

コンポーネントとVuexの連動をStorybookを使って確認したいと思います。

下記のコンポーネントは、Vuexを通じでAPIを叩き(json-server使用)、テキストを取得しています。

こちらをStorybookで再現します。

<img src="/image/storybook/vuexDemo.gif" width="700">

こちらをStorybookで再現します。

まずは、`Vuex`などのモジュール読み込みと、初期化をします。

```js
// Button.stories.js

import Vue from 'vue';
import Vuex, { mapState } from 'vuex';
import axios from 'axios';

// コンポーネントを追加
import LoadingButtonWithMessageComponent from './LoadingButtonWithMessage';

// Vuexの初期化
Vue.use(Vuex);
```

あとは、Storyのコンポーネントに`store`の設定を書くだけです。

```js
// Button.stories.js
export const LoadingButtonWithMessage = () => ({
  components: { LoadingButtonWithMessageComponent },
  template: `
    <LoadingButtonWithMessageComponent
      text="Loading Button with Message"
      :isLoading="isLoadingMessage"
      :message="message"
      :handle-click-button="handleClickFetchMessageButton"
    />`,
  computed: {
    ...mapState({
      isLoadingMessage: state => state.isLoading,
      message: state => state.message,
    })
  },
  methods: {
    handleClickFetchMessageButton() {
      this.$store.dispatch("setMessage", { message: "message" });
    }
  },
  // storeの設定を直接かく
  store: new Vuex.Store({
      state: {
        isLoading: false,
        message: '',
      },
      mutations: {
        setLoading(state, isLoading) {
          state.isLoading = isLoading;
        },
        setMessage (state, { message, isLoading}) {
          state.isLoading = isLoading
          state.message = message
        }
      },
      actions: {
        async setMessage ({ commit }) {
          commit('setLoading', true);
          const { data } = await axios.get('http://localhost:3004/post');
          commit('setMessage', { message: data.message, isLoading: false });
        }
      }
    }),
});
```

storeの設定は長くなりやすいので、別ファイルで書いてimportしても良いかもしれません。

## Addon

Storybookは基本的機能とは別に追加で機能を使うことができます。
その追加機能のことを`Addon`と言います。

公式サイトで紹介されているAddonは下記になります。

[Addon一覧](https://storybook.js.org/docs/addons/addon-gallery/)

Addonは個別にnpmからインストールします。

ここではいくつかのAddonを紹介します。

### Knobs

[Storybook Addon Knobs](https://github.com/storybookjs/storybook/tree/master/addons/knobs)

propsの内容をブラウザ上で操作できます。

#### インストール・設定

```
$ npm install @storybook/addon-knobs --save-dev
```

Addonは基本的に登録設定を行います。
`.storybook/addons.js`というファイルを作り、そちらに設定してしていきます。

```
$ touch .storybook/addons.js
```

```js
// .storybook/addons.js
import '@storybook/addon-knobs/register';
```

登録されると、StorybookにAddonの表示がされます。
<img src="/image/storybook/knobs.png" width="700">

#### Storyに追加

前途した`LoadingButton`の内容を変更してknobsを使ってみます。

まずは、モジュールを読み込みます。

```js
// Button.stories.js

import {
  withKnobs,
  boolean,
  text,
} from '@storybook/addon-knobs';
```

`withKnobs`はStoryでknobsを使うためのモジュールです。
`boolean`や`text`はPropsの型をしているます。

次にデコレータを使ってknobsを使用できるようにします。

```js
// Button.stories.js

export default { title: 'Atoms|Button', decorators: [withKnobs] };

```

その後、コンポーネント側にPropsを設定します。

```js
// Button.stories.js

export const LoadingButton = () => ({
  components: { LoadingButtonComponent },
  template: `<LoadingButtonComponent
    :text="text"
    :isLoading="isLoading"
    :handle-click-button="handleClickLoadingButton"
  />`,
  props: {
    isLoading: {
      type: Boolean,
      default: boolean('IsLoading', false),
    },
    text: {
      type: String,
      default: text('Text', 'This is Loading Button'),
    }
  },
  methods: {
    handleClickLoadingButton(e) {
      this.isLoading = true;
      setTimeout(() => {
        this.isLoading = false;
      }, 2000);
    },
  }
});
```

Propsの`defalt`プロパティで先ほど読み込んだ`boolean`や`text`モジュールを使用しています。ここで指定したPropsをコンポーネントに渡すとStorybook上で操作できるようになります。

<img src="/image/storybook/loading2ButtonKnobs.gif" width="700">

`boolean`や`text`以外にも型設定を行うことができるので、公式サイトをご確認ください。
https://github.com/storybookjs/storybook/tree/master/addons/knobs#available-knobs

### backgrounds

[Storybook Addon Backgrounds](https://www.npmjs.com/package/@storybook/addon-backgrounds)

Storybook全体の背景色をブラウザ上で変更できます。

#### インストール・設定

```
$ npm install @storybook/addon-backgrounds --save-dev
```


```js
// .storybook/addons.js
import '@storybook/addon-backgrounds/register';
```

#### Storyに追加

Story側に追加していきます。

```js
// Button.stories.js

export default {
  title: 'Atoms|Button',
  decorators: [withKnobs],
  // 追加
  parameters: {
    backgrounds: [
      { name: 'light', value: '#eeeeee' },
      { name: 'dark', value: '#222222', default: true },
    ],
  },
};
```

ベースの設定に`parameters`プロパティを追加して、その中に`backgrounds`パラメータを指定します。画面上で設定したい色を指定することができます。

<img src="/image/storybook/addon-backgrounds.gif" width="700">

Propsに応じて色が変わるコンポーネントなどの背景色との色合いを確認する時などに便利です。

### viewport

[Storybook Addon Viewport](https://www.npmjs.com/package/@storybook/addon-viewport)

Storybookないで複数のViewportの切り替えを行えます。

#### インストール・設定

```
$ npm install @storybook/addon-viewport --save-dev
```


```js
// .storybook/addons.js
import '@storybook/addon-viewport/register';
```

`addon-viewport`は登録のみで使用可能です。

<img src="/image/storybook/addon-viewport.gif" width="700">


## HTMLファイルにビルド

これまで、Storybookの簡易サーバーを使ってStoryを作ってきましたが、出来上がったStorybookをHTMLとしてビルドすることもできます。

あらかじめ用意されたコマンド(`build-storybook`)を叩くだけです。

```
$ npx build-storybook
```

上記を叩くと、`index.html`が生成されます。このファイルを使ってデザイナーやディレクターに共有することができます。

`build-storybook`コマンドにはいくつかオプション(出力先指定、ファイル監視など)があるのでこちらもチェックしてみてください。

https://storybook.js.org/docs/configurations/cli-options/#for-build-storybook


## 演習　- Storyを作ってみよう -

これまでの例を参考に実際にStoryを作ってみましょう。
こちらで用意したコンポーネント(※別途勉強会当日までに用意します)もしくは各自が考えるコンポーネントをStoryに追加します。

今回作るStoryとしては、下記が満たしているものを目指します。

- Storybook上でPropsの変更に応じたUIの変化を確認できる
- 何かしらのAddonを使用する
- Vuex + axios + json-serverを使ったStoryを作る　※これは可能であれば