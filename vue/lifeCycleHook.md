# リポジトリ
https://github.com/du-masa/lifeCycleHooks

## ライフサイクルフックとは

vueはインスタンスの生成(new Vue)やコンポーネントの生成時、DOMの更新時、インスタンスやコンポーネントの破棄時など、特定のタイミングで自動的に実行されるメソッドがあります。
それらをライフサイクルフックと呼びます。

初期化時に実行したい処理や、データが変わった時に実行したい処理があった時にこのライフサイクルフックを利用します。

## ライフサイクルフックの種類

- beforeCreate
- created
- beforeMounted
- mounted
- beforeUpdated
- updated
- beforeDestroy
- destroyed

全てを使う必要はなく、状況に応じて必要になったら使って行くことが多いです。
基本`created`か`mounted`をよく使います。


### beforeCreate

インスタンスやコンポーネントの初期化と同時に呼ばれます。
同期的処理

```html
<template>
  <div>
    <div id="child">child</div>
  </div>
</template>

<script>
export default {
  data: function() {
    return {
      data1: 'this is data1'
    }
  },
  beforeCreate: function() {
    console.log('beforeCreate')
  },
}
</script>
```

### created

インスタンスやコンポーネントの初期化後に呼ばれます。

データの監視(データの参照ができる)やイベントの初期化が完了している状態です。
初期データを使って何か処理を行い時などに使うと良いです。
同期的処理

```html
<template>
  <div>
    <div id="child">child</div>
  </div>
</template>

<script>
export default {
  data: function() {
    return {
      data1: 'this is data1'
    }
  },
  beforeCreate: function() {
    console.log('beforeCreate')
  },
  created: function() {
    console.log('created')
    console.log(this.data1) //データの参照ができるようになる
  },
}
</script>
```

### beforeMount

インスタンスやコンポーネントがマウントされる前に呼ばれます。
つまり、ブラウザにレンダリングされる前です。

```html
<template>
  <div>
    <div id="child">child</div>
  </div>
</template>

<script>
export default {
  data: function() {
    return {
      data1: 'this is data1'
    }
  },
  beforeCreate: function() {
    console.log('beforeCreate')
  },
  created: function() {
    console.log('created')
    console.log(this.data1) //データの参照ができるようになる
  },
  beforeMount: function() {
    console.log('beforeMount')
  },
}
</script>
```

### mounted

インスタンスやコンポーネントがマウントされた後に実行されます。
この段階では、ブラウザへのレンダリングが完了しているため、
`document.getElementById()`などのDOM要素へのアクセスが可能になります。

```html
<template>
  <div>
    <div id="child">child</div>
  </div>
</template>

<script>
export default {
  data: function() {
    return {
      data1: 'this is data1'
    }
  },
  beforeCreate: function() {
    console.log('beforeCreate')
  },
  created: function() {
    console.log('created')
    console.log(this.data1) //データの参照ができるようになる
  },
  beforeMount: function() {
    console.log('beforeMount')
  },
  mouted: function() {
    console.log('mouted')
    console.log(document.getElementById('child')) //DOM要素にアクセスできる
  }
}
</script>
```

### beforeUpdate

データの更新になどによってDOMの再描画が行われる前に実行される。
`updateChildrenData2`メソッドが呼ばれて値が更新されると、実行されます。

```html
<template>
  <div>
    <div id="child">child {{data2}}</div>
    <button @click="updateChildrenData2"> update children data2</button>
  </div>
</template>

<script>
export default {
  data: function() {
    return {
      data1: 'this is data1',
      data2: false
    }
  },
  methods: {
    updateChildrenData2: function() {
      this.data2 = !this.data2
    }
  },
  beforeCreate: function() {
    console.log('beforeCreate')
  },
  created: function() {
    console.log('created')
    console.log(this.data1)
  },
  created: function() {
    console.log('created')
    console.log(this.data1) //データの参照ができるようになる
  },
  beforeMount: function() {
    console.log('beforeMount')
  },
  mouted: function() {
    console.log('mouted')
    console.log(document.getElementById('child')) //DOM要素にアクセスできる
  },
  beforeUpdate: function() {
    console.log('beforeUpdate')
  },
}
```

### updated

データの更新になどによってDOMの再描画が行われた後に実行されます。
状態変更後のDOM要素にアクセスする場合は、updated内で取得すると良いです。
ただ、updated内で状態を更新すると無限ループになる場合があるので注意が必要です。

```html
<template>
  <div>
    <div id="child">child {{data2}}</div>
    <button @click="updateChildrenData2"> update children data2</button>
  </div>
</template>

<script>
export default {
  data: function() {
    return {
      data1: 'this is data1',
      data2: false
    }
  },
  methods: {
    updateChildrenData2: function() {
      this.data2 = !this.data2
    }
  },
  beforeCreate: function() {
    console.log('beforeCreate')
  },
  created: function() {
    console.log('created')
    console.log(this.data1)
  },
  created: function() {
    console.log('created')
    console.log(this.data1) //データの参照ができるようになる
  },
  beforeMount: function() {
    console.log('beforeMount')
  },
  mouted: function() {
    console.log('mouted')
    console.log(document.getElementById('child')) //DOM要素にアクセスできる
  },
  beforeUpdate: function() {
    console.log('beforeUpdate')
  },
  updated: function() {
    console.log('updated')
  }
}
```

### beforeDestroy

インスタンスやコンポーネントが破棄される前に実行されます。
破棄されるのは、v-ifでコンポーネントが非表示になる時や、SPAでページ遷移する際など、対処となるコンポーネントがDOMから削除される状態と思ってもらえれば良いかと思います。
ただし、SPAでなく単純にサーバーにリクエストを送る際のページ遷移時は実行されません。


```html
<template>
  <div>
    <div id="child">child {{data2}}</div>
    <button @click="updateChildrenData2"> update children data2</button>
  </div>
</template>

<script>
export default {
  data: function() {
    return {
      data1: 'this is data1',
      data2: false
    }
  },
  methods: {
    updateChildrenData2: function() {
      this.data2 = !this.data2
    }
  },
  beforeCreate: function() {
    console.log('beforeCreate')
  },
  created: function() {
    console.log('created')
    console.log(this.data1)
  },
  created: function() {
    console.log('created')
    console.log(this.data1) //データの参照ができるようになる
  },
  beforeMount: function() {
    console.log('beforeMount')
  },
  mouted: function() {
    console.log('mouted')
    console.log(document.getElementById('child')) //DOM要素にアクセスできる
  },
  beforeUpdate: function() {
    console.log('beforeUpdate')
  },
  updated: function() {
    console.log('updated')
  },
  beforeDestory: function() {
    console.log('beforeDestory')
  }
}
```

上記例では、コンポーネントを非表示にすると`beforeDestory`メソッドが発動されます。

### destroyed

インスタンスやコンポーネントが破棄された後に実行されます。

```html
<template>
  <div>
    <div id="child">child {{data2}}</div>
    <button @click="updateChildrenData2"> update children data2</button>
  </div>
</template>

<script>
export default {
  data: function() {
    return {
      data1: 'this is data1',
      data2: false
    }
  },
  methods: {
    updateChildrenData2: function() {
      this.data2 = !this.data2
    }
  },
  beforeCreate: function() {
    console.log('beforeCreate')
  },
  created: function() {
    console.log('created')
    console.log(this.data1)
  },
  created: function() {
    console.log('created')
    console.log(this.data1) //データの参照ができるようになる
  },
  beforeMount: function() {
    console.log('beforeMount')
  },
  mouted: function() {
    console.log('mouted')
    console.log(document.getElementById('child')) //DOM要素にアクセスできる
  },
  beforeUpdate: function() {
    console.log('beforeUpdate')
  },
  updated: function() {
    console.log('updated')
  },
  beforeDestory: function() {
    console.log('beforeDestory')
  },
  destroyed: function() {
    console.log('destroyed')
  }
}
```
