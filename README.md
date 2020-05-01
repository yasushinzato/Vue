Vueについて学んだ内容

## 算出プロパティ
複数回処理を実行する場合は算出プロパティを利用する。
メソッドも同じ処理を実行するのに利用できるが、以下のような違いがあり、
算出プロパティはリアクティブな依存関係に基づきキャッシュされる。
リアクティブな依存関係が更新されたときだけ再評価される仕組み。

### 違い①：（）の有無
- computed ・・・ ()は不要
- methods ・・・ ()は必要

### 違い②：getter, setter
- computed ・・・getter, setter
- methods ・・・ getter

### 違い③：キャッシュ
- computed ・・・キャッシュされる
- methods ・・・ キャッシュされない

#### 消費税ソースコードsetter,getter
<details>
<summary>
ソースコード（……）
</summary>
html
```html
<div id='app'>
  <p>
    base price: <input type="text" v-model="basePrice">
  </p>
  <p>
    tax include price: <input type="text" v-model="taxIncludedPrice">
  </p>
</div>
<script src="https://cdn.jsdelivr.net/npm/vue@2.5.16/dist/vue.js"></script>
```
Vue
```javascript
var app = new Vue({
	el: '#app',
	data: {
  	basePrice: 100
  },
/*   算出プロパティ　*/ 
	computed: {
  	taxIncludedPrice: {
/*     getter */
    	get: function() {
      	return parseInt(this.basePrice * 1.08)
      },
/*       setter */
      set: function(taxIncludedPrice) {
      	this.basePrice = Math.ceil(taxIncludedPrice / 1.08)
      }
    }
  }
})
```
}}

#### ランダム（キャッシュ）
{{collapse(ソースコード…)
html
```html
<div id='app'>
  <h2>
    Computed
  </h2>
  <ol>
<!--   キャッシュされるので、全部おなじ数字が表示される。 -->
    <li>{{ computedNumber }}</li>
    <li>{{ computedNumber }}</li>
    <li>{{ computedNumber }}</li>
  </ol>
  <h2>
    Methods
  </h2>
  <ol>
<!--   キャッシュされないので、全部違う数字が表示される。 -->
    <li>{{ methodsNumber() }}</li>
    <li>{{ methodsNumber() }}</li>
    <li>{{ methodsNumber() }}</li>
  </ol>
</div>
<script src="https://cdn.jsdelivr.net/npm/vue@2.5.16/dist/vue.js"></script>
```
Vue
```javascript
var app = new Vue({
	el: '#app',
	computed: {
  	computedNumber: function() {
    	console.log('computed!')
      return Math.random()
    }
  },
  methods: {
  	methodsNumber: function() {
    	console.log('methods!')
      return Math.random()
    }
  }
})
```
</details>

## 監視プロパティ（ウォッチャ）とは
- 特定のデータまたは、算出プロパティの状態を監視して、変化があったときに登録した処理を自動的に実行する。
- 事例）検索フォームの値が変わったタイミングで自動的にAjax通信を行って一覧を表示

<details>
<summary>
ソースコード（……）
</summary>
html
```html
<div id='app'>
  <p>
    {{ message }}
  </p>
  <p>
    <input type="text" v-model:value="message">
  </p>
  <pre> {{ $data }}</pre>
</div>
<script src="https://cdn.jsdelivr.net/npm/vue@2.5.16/dist/vue.js"></script>
```
Vue
```javascript
var app = new Vue({
	el: '#app',
	data: {
  message: 'Helo Vuejs'
  },
  watch: {
  	message: function(newValue, oldValue) {
    	console.log('new: %s, old: %s', newValue, oldValue)
    }
  }
})
```
</details>

## クラスとスタイルのバインディング
CSSクラスの設定をバインディングで指定する。

- 真偽値による制御
- 変数にCSSクラスを代入
- 配列で複数のバインディング
- 上記の個別指定ではなく、オブジェクトによる一括指定
- 三項演算子での適用
- CSSではなく、インラインスタイルでの指定方法
- インラインスタイルのオブジェクト指定

<details>
<summary>
ソースコード（……）
</summary>
html
```html
<div id="app">
  <p>
<!-- CSSクラスの適用を真偽値変数で制御する -->
<!--   CSSクラスにハイフンがある場合はシングルコーテーションで囲うこと。 -->
<!-- 通常のCSSクラス（プレーンなクラス属性）とVueのクラスを共存させることが可能。優先は通常のCSSと同様に順番どおりに適用される。 -->
      Hello <span class="bg-gray text-blue" v-bind:class="{large: isLarge, 'text-danger': hasError}">Vue.js</span>
  </p>
<!-- HTML側で配列構文を入れることでCSSクラスを適用する。 -->
  <p>
     Hello <span v-bind:class="[largeClass, dangerClass]">Vue.js!</span>
  </p>
<!--   CSSクラスの指定をオブジェクトで一括指定する。 -->
  <p>
    Hello <span v-bind:class="classObject">Vue.js</span>
  </p>

<!-- 三項演算子により、CSS適用を制御する。常に適用したいCSSクラスは配列構文を利用 -->
  <p>
    Hello <span v-bind:class="[isLarge ? largeClass : '', dangerClass]">Vue.js</span>
  </p>
  
<!-- インラインスタイルのデータバインディング.styleの場合はキャメルケースで指定する.若しくはシングルコーテーションで通常のプラパティ名を指定できる-->
  <p>
    Hello <span v-bind:style="{ color: vcolor, fontSize: vfontSize + 'px' }">Vue.js</span>
  </p>
<!--   インラインスタイルのオブジェクト指定 -->
  <p>
    Hello <span v-bind:style="styleObject">Vue.js</span>
  </p>
  
  

</div>

<script src="https://cdn.jsdelivr.net/npm/vue@2.5.16/dist/vue.js"></script>
```
CSS
```css
.large {
  font-size: 36px;
}

.text-danger {
  color: red;
}

.bg-gray {
  background-color: gray;
}

.text-blue {
  color: blue;
}
```
Vue
```javascript
var app = new Vue({
	el: '#app',
  data: {
    // CSSクラスを適用するかどうか真偽値変数で制御する
   	isLarge: true,
  	hasError: true,  
    // 適用したいCSSクラスを指定する
    largeClass: 'large',
    dangerClass: 'text-danger',
    // html側でのCSSクラス指定をオブジェクトで一括指定する。
    classObject: {
    	large: true,
      'text-danger' : true
    },
    // 三項演算子
    largeClass: {
      large: true,
      'bg-gray': true,      
    },
    dangerClass: {
    	'text-danger': true
    },
    isLarge: true,
    // インラインスタイルのデータバインディング
    vcolor: 'blue',
    vfontSize: 36,
    // インラインスタイルのオブジェクト指定
    styleObject: {
    	color: 'green',
      fontSize: '36px'
    }
  }
})
```
</details>


## 条件付きレンダリング

条件付きレンダリングの`v-if`と`v-show`の違いについて

- v-if
  - 要素をDOMから削除・追加
  - 高い切り替えコスト
  - v-else, v-else-ifが使える

実行時に条件を変更することが殆どない場合に使うといい。

- v-show
  - CSS display プロパティ
  - 高い初期描画コスト
  - v-else, v-else-ifが使えない

表示・非表示を多く繰り返すときに使うといい。

<details>
<summary>
ソースコード（……）
</summary>
以下は参考ソース
html
```html
<div id="app">

<!-- if条件分岐の書き方 -->
  <p v-if="color === 'red'">
    Stop
  </p>
  <p v-else-if="color === 'yellow'">
    Caution
  </p>
  <p v-else-if="color === 'blue'">
    Go
  </p>
  <p v-else>
    Not red/yellow/blue
  </p>

<!-- v-showの書き方 -->
  <p v-show="toggle">
    Hello Vue.js
  </p>

</div>

<script src="https://cdn.jsdelivr.net/npm/vue@2.5.16/dist/vue.js"></script>
```
Vue
```javascript
var app = new Vue({
	el: '#app',
	data: {
  	color: 'blue',
    toggle: false
  }
})
```
</details>


## イベントハンドリング
イベント処理について
`v-on`ディレクティブを使うことで、DOMイベントの購読、イベント発火時のJavascript実行が可能。


#### イベント修飾子の種類
|名称|内容|
|:-|:-|
|.stop|イベントの親要素への伝搬を中止|
|.prevent|イベント規定の動作をキャンセル|
|.capture|イベントハンドラをキャプチャモードで動作|
|.self|イベント発生元が要素実親の場合にだけ実行|
|.once|イベントハンドラを1回だけ実行|
|.passive|passiveモードを有効化|
※修飾子は関連コードが同じ順序で生成される。
`v-on:click.prevent.self`は全クリックイベントを防ぐ。
`v-on:click.self.prevent`は要素自身におけるクリックイベントを防ぐ。


#### キー修飾子
キーイベントをフックに、特定のキーコードのキーが押されたときのみ、特定のイベントハンドラを呼び出すことができる。
グローバルな`config.keyCodes`オブジェクト経由でカスタムキー修飾子のエイリアスを定義できる。
```javascript
// `v-on:keyup.f1` を可能にします
Vue.config.keyCodes.f1 = 112
```
マウスボタンの修飾子もある。

#### システム修飾子
Ctrlキーが押されているとき、altキーが押されているとき、shiftキーが押されているとき、ウィンドウズキー(meta)が押されているときの修飾子。

<details>
<summary>
ソースコード（……）
</summary>
html
```html
<div id="app">
  <p>
    {{ counter }}
  </p>
  <p>
    {{ message }}
  </p>
  <p>
    {{ messageOnce }}
  </p>
  <p>
<!--   ESCキー（27）を押すと、clearメソッドを実行する.エイリアスでの指定も可能.Orによる複数キーの指定も可能 -->
<!--     <input type="text" v-on:keyup.27='clear' v-model="keymessage"> -->
<!--     <input type="text" v-on:keyup.esc.space.up='clear' v-model="keymessage"> -->
<!--     Ctrl+C　のキー組み合わせにより、クリアーメソッドを実行。Cのキーコードは67 -->
    <input type="text" v-on:keyup.ctrl.67='clear' v-model="keymessage">
    <br>
    {{ keymessage }}
    <br>
    Ctrl + c でテキスト内容をクリア
      <br>
    <div v-on:click.shift="clickDiv">
      Click Me!!(Shift + click)
    </div>
  </p>
<!--   インラインメソッドハンドラ -->
<!--   <button v-on:click="counter++">
    Click!
  </button> -->
<!--   メソッドイベントハンドラ -->
  <button v-on:click="clickHandler($event, 'Vue.js')">
    Click2!
  </button>
<!--   Once修飾子.　修飾子はHTML側で指定する -->
  <button v-on:click.once="clickOnceHandler">
    Now!
  </button>
<!--   v-onを＠に省略できる -->
  <h2>
    v-on省略記法の確認
  </h2>
  <button v-on:click="clickAbbreviation">
    通常記法
  </button>
  <button @click="clickAbbreviation">
    @省略記法
  </button>
</div>

<script src="https://cdn.jsdelivr.net/npm/vue@2.5.16/dist/vue.js"></script>
```
Vue
```javascript
var app = new Vue({
	el: '#app',
	data: {
  	counter: 0,
    message: '',
    messageOnce: '',
    keymessage: ''
  },
  methods: {
  	// メソッドイベントハンドラ
  	clickHandler: function($event,message) {
    	this.counter++
      this.message = message
      // イベントオブジェクトの参照
      console.log($event)
      console.log($event.target.tagName)
      console.log($event.target.innerHTML)
      console.log($event.target.type)
    },
    // 通常のメソッドとなっている。Once修飾子はHTMLで指定。
    clickOnceHandler: function() {
    	this.messageOnce = new Date().toLocaleTimeString()
    },
    clear: function() {
    	this.keymessage = ''
    },
    // キーイベント処理の確認用
    clickDiv: function() {
    	alert('shift + Click')
    },
    // 省略記法の確認
    clickAbbreviation() {
    	alert('＠省略記法の確認')
    }
  }
})
```
</details>


## コンポーネント
コンポーネントは名前付きの再利用可能なVueインスタンスのこと。
同じコンポーネントを複数作成しても、それぞれインスタンスは別々なので、保持するデータもインスタンス毎に異なる。

コンポーネント名にはハイフンを1つ以上含むケバブケースを利用しないといけない。
理由は、HTML要素の衝突を防止するため。

コンポーネントのdataオプションはfunctionである必要がある。
また、単一の要素でないといけないので、divで囲うなどの注意が必要

<details>
<summary>
ソースコード（……）
</summary>
html
```html
<div id="app">
<!--   <hello-component></hello-component>
  <hello-component></hello-component>
  <hello-component></hello-component> -->
  <button-counter></button-counter>
    <button-counter></button-counter>
</div>

<script src="https://cdn.jsdelivr.net/npm/vue@2.5.16/dist/vue.js"></script>
```
Vue
```javascript
// コンポーネント　グローバル登録
/* Vue.component('hello-component', {
  template: '<p>Hello<p>'
})
 */

// コンポーネント　ローカル登録
/* var helloComponent = {
  template: '<p>Hello</p>'
} */

// ボタンクリックのカウントアップする
Vue.component('button-counter', {
	data: function() {
  	return {
    	count: 0
    }
  },
  template: '<button v-on:click="count++">{{ count }}</button>'
})

var app = new Vue({
	el: '#app',
  // コンポーネント　ローカル登録の場合は以下で定義する。
/*  components: {
    'hello-component' : helloComponent
  }  */
})
```
</details>


## Vue Router

URLの#を取り除く場合は、HTML5 Historyモードを使うことで取り除ける。
routerのmodeオプションにhistoryを指定する。
Webサーバ毎に設定は異なるので、注意が必要。

[https://router.vuejs.org/ja/guide/essentials/history-mode.html#サーバーの設定例]


## Vue CLI3導入

CLIでプロジェクト雛形作成、JSファイルを一つにまとめる、.vueファイルをjsに変換、
トランスパイル（旧いES5に変換する）などができる。

- Nodeインストール
- Vue CLIインストール  
  `npm install -g @vue/cli@3.9.3`でインストール。`vue --version`
- プロジェクト作成  
  `vue create vue-project-sample` のコマンド後、対話形式で  
  Manually select features  
  Babel, Router, Vuex
  Use history → Yes  
  ファイルの設定ファイルは別で管理するので、Indedicated   
設定内容をpresetとして保存する必要はないので、N

