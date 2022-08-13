# VueやReactとの迅速・複雑な統合シナリオに対応するためのツールキット
<div align=center>
  <img src="https://raw.githubusercontent.com/devilwjp/VueReact/master/vuereact-combined.png"/>
</div>  

<div align=center>
  <p>
  <h4>Vue・Reactで作成されたコンポーネントをVue・Reactのプロジェクトで使用 </h4>
  <p>
</div>  

## Vue3対応してる?
Vue3とReactを使う場合は、以下[Veaury](https://github.com/devilwjp/veaury)

## インストール  
````  
npm i vuereact-combined -S
````  

## Why?  
#### vueとreactを同プロジェクト・同ページ・同コンポーネントで扱うことができる  
+ vueとreactの両方の技術スタックを使用可能にし、よりモバイル性の高いプロジェクトにする  
+ サードパーティプラグインの使用を選択でき、vueとreactの両方のプラグインを共通化する  
+ 技術スタックに制限されることなく、開発者間の技術的なコミュニケーションを促進することができる  
+ 他のvueやreactプロジェクトから優れたコードを迅速に取り込むことができるようにする  
+ vueとreact、両者の本質を理解できるようにし、チームのフロントエンド技術スタックの幅を広げることを促進する  
+ 非常に使いやすい  
## Benchmark
VueとReactを融合するアイデアはvueraのおかげである。  
しかし、vueraは非常に基本的なコンポーネントの融合しか解決できていなかった。  
slot（子）やデータ変更時のレンダリングパフォーマンスに問題があり、複雑なシナリオや本番環境では使用できなかった。  
vuereact-combinedは融合を極限まで進め、VueとReactコンポーネントのほとんどの機能をサポートし、vueraとは異なるアプローチでレンダリングの更新を行い、レンダリング性能の問題を完璧に解決している。  

シーン／機能 | vuereact-combined | vuera |  
| --------- | --------- | --------- |
normal prop (vue / react) | ✔ | ✔ |  
event (vue / react) | ✔ | ✔ |  
children (vue / react) | ✔ | ✔ |  
Provider/Consumer in vue (react) | ✔ |  |  
Provider/Consumer cross react->vue->...->react | ✔ |  |
provide/inject cross vue->react->...->vue | ✔ |  |  
named slots (vue) | ✔ |  |  
scope slots (vue) | ✔ |  |  
v-model (vue) | ✔ |  |  
sync (vue) | ✔ |  |  
render props (react) | ✔ |  |  
node props (react) | ✔ |  |  
enter & leave イベント委譲 (react) | ✔ |  |  
slots & children & 親コンポーネントのデータ変更後のnodeのライフサイクル動作 | トリガーアップデート | 作成と破棄を毎回トリガーする |  
vuex in react | ✔ |  |  
vue-router in react | ✔ |  |  
redux in vue | ✔ |  |  
react-router in react | ✔ |  |  
lazyReactInVue | ✔ |  |  
lazyVueInReact | ✔ |  |  
フレームワーク間で使用されるサードパーティコンポーネント（例：antd、element） | 全てのサードパーティ製コンポーネントをサポート | 基本的にサポート外 |  
融合レイヤーでのdom attrのカスタマイズ | ✔ |  |    

## HOC(高階コンポーネント)の場合（HOC： あるコンポーネントを受け取って新規のコンポーネントを返すような関数） 
````vue
<!--Vue File-->
<template>
  <Popover content="I am React Popover" title="Title">
    <Button type="primary">It's Vue Button</Button>
  </Popover>
</template>

<script>
import { applyReactInVue } from 'vuereact-combined'
// antd React
import { Popover } from 'antd'
// element-ui Vue
import { Button } from 'element-ui'

export default {
  components: {
    // applyReactInVue（HOC）を使用しantd PopoverをVueコンポーネントに変換
    Popover: applyReactInVue(Popover),
    Button,
  },
}
</script>

<style scoped>

</style>
````  
````jsx
// React JSX File
import React, { useState } from 'react'
// element-ui DatePicker Vue
import { DatePicker } from 'element-ui'
import { applyVueInReact } from 'vuereact-combined'

// applyVueInReact（HOC）を使用し、elementUIのDatePickerをReactコンポーネントに変換
const ElDatePicker = applyVueInReact(DatePicker)
export default function() {
  const [timeValue, setTimeValue] = useState(Date.now())
  return <ElDatePicker
    {/* ReactにおけるVueコンポーネントのv-modelの使用法 */}
    $model={{
        value: timeValue,
        setter: (val) => { setTimeValue(val) },
    }}
    type="date"
    placeholder="日付を選択"/>
}

````
## 利用シーン
最も基本的なレベルでは、少なくとも `vue@^2.6`, `react@^16.3`, `react-dom@^16.3` がプロジェクトに存在する必要があります。  
### サードパーティのReactコンポーネントをVueのプロジェクトで使用する  
サードパーティのreactコンポーネントは、すでに `babel` で処理されており、Reactの `jsx` は含まれていません。   
そのため、applyReactInVueを使用して、サードパーティ製のReactコンポーネントをプロジェクト内で直接処理することができます。
### ReactプロジェクトでサードパーティのVueコンポーネントを使用する。  
Reactの場合と同じく、サードパーティのVueコンポーネントは、すでに `vue-loader` と `babel` で処理されていて、 `.vue` ファイルやVueの `jsx` は含まれていません。   
そのため、サードパーティのVueコンポーネントは、applyVueInReactを使用してプロジェクト内で直接処理することができます。    
### 複雑なケース（プロジェクトにreactとvueの両方の環境がインストールされ、設定されている場合） 
この場合、ReactとVueの両方のコンポーネントコードを単一のプロジェクトで開発・記述できます。 両方の技術スタックが依存する環境を用意する必要があるため、プロジェクトのビルド（通常は `webpack`）と   `babel.config.js` の設定をいくつか変更する必要があります。 
以下の例を参考にしてください。   
+ プロジェクトが vue-cli3 で作成された場合  
https://github.com/devilwjp/vuereact-for-vuecli3-demo
+ react-create-appでプロジェクトを作成した場合(reactのバージョンは16.3以上である必要あり) 
https://github.com/devilwjp/vuereact-for-cra-demo  
  
## パス属性（アトリビュートパス）  
Reactで通常のReactの方法と同じように、Vueのコンポーネントに対してプロパティとchildrenを渡す
````jsx
// React JSX File
import React, { useState } from 'react'
// element-ui Vue
import { Button, ButtonGroup } from 'element-ui'
import { applyVueInReact } from 'vuereact-combined'

const ElButton = applyVueInReact(Button)
const ElButtonGroup = applyVueInReact(ButtonGroup)

export default function() {
    
  const [type, setType] = useState('primary')
  const [disabled, setDisabled] = useState(false)
  const [content, setContent] = useState('提出')
    
  return <ElButtonGroup>
    <ElButton type="danger" disabled>提出</ElButton>
    <ElButton type={type} disabled={disabled}>提出</ElButton>
    <ElButton type="danger">{content}</ElButton>
  </ElButtonGroup>
}
````  
VueでReactのコンポーネントに対してプロパティやslotを渡すのは、Vueを使った通常の方法と同じです。  
````vue
<!--Vue File-->
<template>
  <Popover :content="content" :title="title">
    {{popoverChildren}}
  </Popover>
</template>

<script>
import { applyReactInVue } from 'vuereact-combined'
// antd React
import { Popover } from 'antd'

export default {
  data() {
    return {
      content: 'I am React Popover',
      title: 'Title',
      popoverChildren: `hover me!`,
    }
  },
  components: {
    // applyReactInVue（HOC）でantd PopoverをVueコンポーネントに変換
    Popover: applyReactInVue(Popover)
  },
}
</script>

<style scoped>

</style>
````  
## ReactでVueコンポーネントのv-modelとsync修飾子を使う
````jsx
// React JSX File
import React, { useState } from 'react'
// element-ui DatePicker Vue
import { DatePicker } from 'element-ui'
// プロパティにsync修飾子を持つVueコンポーネント
import VueComponent from './VueComponent.vue'
import { applyVueInReact } from 'vuereact-combined'

const ElDatePicker = applyVueInReact(DatePicker)
const VueComponentInReact = applyVueInReact(VueComponent)

export default function() {
  const [timeValue, setTimeValue] = useState(Date.now())
  const [timeValue1, setTimeValue1] = useState(Date.now())
  // ReactにおけるVueコンポーネントのv-modelの使用法
  const $model = {
    value: timeValue,
    setter: (val) => { setTimeValue(val) },
  }
  // ReactでのVueコンポーネント同期の利用
  const $sync = {
    props1: {
      value: timeValue1,
      setter: (val) => { setTimeValue1(val) },
    }
  }
  return <div>
    <ElDatePicker $model={$model} type="date" placeholder="日付を選択"/>
    <VueComponentInReact $sync={$sync} />
  </div>
}
````  
`$model`を使ったオプジェクトの受け渡し  
`$model`  
**Type:** `{value: state, setter: (val: nextState) => void}`  
`value`はv-modelに渡される値の状態，`setter`は値の状態を変更するために子コンポーネントから親コンポーネントに送られるトリガー関数である。  
`$sync`  
**Type:** `{[propName: {value: state, setter: (val: nextState) => void}]}`  

## ReactでVueのコンポーネントを使うためのScoped slotと名前付きslot  
```jsx
// React JSX File
import React, { useState } from 'react'
// Scoped slotと名前付きslotをプロパティに持つVueコンポーネント
import VueComponent from './VueComponent.vue'
import { applyVueInReact } from 'vuereact-combined'

const VueComponentInReact = applyVueInReact(VueComponent)
export default function() {
  // 名前つきslot
  const $slots = {
      slotA: <div>名前つきslotA</div>,
      slotB: <div>名前つきslotB</div>
  }
  // Scoped slot
  const $scopedSlots = {
      slotC: (context) => <div>Scoped slotC：{context.value}</div>
  }
  return <div>
    <VueComponentInReact $slots={$slots} $scopedSlots={$scopedSlots}>
      <h1>私は普通のslotです。</h1>
    </VueComponentInReact>
  </div>
}
```  
`$slots` 名前付きslotのプロパティ
**Type:** {[slotName: string]: ReactNode}  
`$scopedSlots` Scoped slotのプロパティ  
**Type:** {[slotName: string]: (context: RenderPropsContext) => ReactElement | ReactComponent}  
## VueコンポーネントでReactNode型プロパティとrenderProps型プロパティをReactコンポーネントに渡す  
```vue
<!--Vue File-->
<template>
  <ReactComponentInVue>
    通常のchildren
    <!--  Reactコンポーネントの場合 slotA={<span>私はslotAプロパティを持つReactNode型です</span>}   -->
    <template v-slot:slotA>
      <span>私はslotAプロパティを持つReactNode型です</span>
    </template>
    <!--  Reactコンポーネントの場合 slotB={<span>私はslotBプロパティを持つReactNode型です</span>}  -->
    <template v-slot:slotB>
      <span>私はslotBプロパティを持つReactNode型です</span>
    </template>
    <!--  Reactコンポーネントの場合 slotC={(context) => <span>私はrenderProps型です：{{context.value}}</span>}  -->
    <template v-slot:slotC="context">
      <span>私はrenderProps型です：{{context.value}}</span>
    </template>
  </ReactComponentInVue>
</template>

<script>
import { applyReactInVue } from 'vuereact-combined'
// ReactNode型プロパティとrenderProps型プロパティを持つReactコンポーネント
import ReactComponent from './ReactComponent'
export default {
  components: {
    ReactComponentInVue: applyReactInVue(ReactComponent)
  }
}
</script>
```  
applyReactInVueは、ReactNode型のプロパティをVueの名前付きスロットに変換。
renderProps型のプロパティをスコープスロットに変換。
名前付きslotとScoped slotは、スロット名がプロパティ名となります。  
## VueコンポーネントでReactコンポーネントのContext/Providerを呼び出す  
```vue
<!--Vue File-->
<template>
  <MyProvider :value="content">
    <Button>Vueのボタン</Button>
    <!--  ReactコンポーネントはContextを通常通り使用することができます  -->
    <ReactComponentInVue/>
  </MyProvider>
</template>

<script>
import { applyReactInVue } from 'vuereact-combined'
// React Context
import MyContext from "./MyContext"
import {Button} from 'element-ui'
import ReactComponent from './ReactComponent'
export default {
  data() {
    return {
      content: 'hahahahaha!'
    }
  },
  components: {
    Button,
    ReactComponentInVue: applyReactInVue(ReactComponent),
    // ProviderをReactコンポーネントとして変換
    MyProvider: applyReactInVue(MyContext.Provider),
  }
}
</script>
```  
## VueContainer，ReactのコンポーネントでVueの動的コンポーネントを利用する  
VueContainerは、Vueコンポーネントをcomponentプロパティで直接レンダリングするHOCです。  
```jsx
// React JSX File
import React, { useState, useEffect } from 'react'
import VueComponent1 from './VueComponent1.vue'
import VueComponent2 from './VueComponent2.vue'
import { VueContainer } from 'vuereact-combined'

const ElButton = applyVueInReact(Button)
const ElButtonGroup = applyVueInReact(ButtonGroup)

export default function() {
  const [vueComponent, setVueComponent] = useState(VueComponent1)
  useEffect(() => {
    // 3秒後にVueComponent2に切り替わる
    setTimeout(() => {
      setVueComponent(VueComponent2)
    }, 3000)
  }, [])
  const prop1 = '属性1'
  const prop2 = '属性2'
  return <div>
      <VueContainer component={vueComponent} prop1={prop1} prop2={prop2}/>
      {/* component属性は、vueのグローバルコンポーネントの使用を示すstring型であり、次の例は、reactコンポーネントでvue-routerを使用することを示した例です。<router-view/> */}
      <VueContainer component="RouterView"/>
  </div>
}
```  

## ReactコンポーネントでVueコンポーネントのイベントを利用する  
注意：onEventプロパティ受け渡しメソッドは、リネームされるプロパティを持っている可能性が否定できないため、ここでは使用しません。  
```jsx
// React JSX File
import React, { useState } from 'react'
// 特定のイベントが開かれているVueコンポーネント
import VueComponent from './VueComponent.vue'
import { applyVueInReact } from 'vuereact-combined'

const VueComponentInReact = applyVueInReact(VueComponent)

export default function() {
    const click = () => {
        console.log('click')
    }
    const mouseEnter = () => {
        console.log('mouseEnter')
    }
    const customEvent = () => {
        console.log('mouseEnter')
    }
    // イベントに対応する関数をv-onプロパティでvueコンポーネントに渡す
    // 以下のコードは、vueのv-onと同じです。 v-on="{click, mouseEnter, customEvent}"
    return <VueComponentInReact on={{click, mouseEnter, customEvent}}/>
}
```  
## applyRedux
作用：使得所有的Vue组件可以使用redux的状态管理
对工具包开启redux状态管理，这个场景一般存在于以React为主的项目中，为了使Vue组件也可以共享到redux，需要在项目的入口文件引入applyRedux方法（整个项目应该只引一次），将redux的store以及redux的context作为参数传入（或者至少在redux的Provider高阶组件引入的地方使用applyRedux方法）
````js  
// 第二个参数是redux的context，之所以需要传第二个参数，是因为有如下场景
// Provider -> ReactCom1 -> VueCom1 -> ReactCom2
// Provider无法直接透过Vue组件传递给之后的React组件，所以applyRedux提供了第二个参数，作用就是可以使通过Vue组件之后的React组件继续可以获取到redux的context
import { ReactReduxContext } from 'react-redux'
import store from '../reactComponents/reduxStore'
applyRedux({ store, ReactReduxContext })
````  
#### store.js
````js  
// 原生的redux store的创建方式
import { createStore } from 'redux'
import someCombineReducer from './reducer' // 建议通过react-redux的combineReducer输出
let store = createStore(someCombineReducer)
export default store
````  
React组件连接redux的方式这里就不再做介绍了，应该使用react-redux的connect方法  
这里介绍Vue组件如何使用redux，工具包尽可能的实现了vue组件使用vuex的方式去使用redux，通过vm.$redux可以在组件实例里获取到redux状态管理
````html  
<template>
  <div>
    redux状态testState1: {{$redux.state.testState1}}
  </div>
</template>

<script>
export default {
  name: 'demo3',
  mounted () {
    // 打印redux的testState2状态值
    console.log(this.$redux.state.testState2)
    // 五秒后将testState1修改成8888
    // 需要在reducer里存在一个action的type为test1可以修改testState1
    // 这里需要按照标准的redux的action标准（必须有type）触发dispatch
    setTimeout(() => {
      this.$redux.dispatch({
        type: 'test1',
        value: 8888
      })
    }, 5000)
  }
}
</script>
````  

## applyVuex
作用：使得所有的Redux组件可以使用Vuex的状态管理  
对工具包开启vuex状态管理，这个场景一般存在于以Vue为主的项目中，为了使React组件也可以共享到vuex，需要在项目的入口文件引入applyVuex方法（整个项目应该只引一次），将vuex的store作为参数传入
````js  
import store from '../store' // vuex的store文件
applyVuex(store)
````  

## connectVuex
类似react-redux的connect方法，在React组件中使用，由于vuex的关键字比redux多，所以将参数改成了对象，包含了mapStateToProps、mapCommitToProps、mapGettersToProps、mapDispatchToProps，每个都是一个纯函数，返回一个对象（和redux的connect使用方式完全一致）
````js  
export default connectVuex({
  mapStateToProps (state) {
    return {
      vuexState: state,
      state1: state.state1,
      moduleAstate: state.moduleA
    }
  },
  mapCommitToProps (commit) {
    return {
      vuexCommit: commit
    }
  },
  // mapGettersToProps = (getters) => {},
  // mapDispatchToProps = (dispatch) => {},
})(ReactComponent)
````  

## lazyVueInReact
在React的router里懒加载Vue组件
````jsx harmony  
import React, { lazy, Suspense } from "react"
import { lazyVueInReact } from 'vuereact-combined'
const Hello = lazy(() => import("./react_app/hello"));
//懒加载vue组件
const TestVue = lazyVueInReact(() => import("./vue_app/test.vue"))


export default [
{
    path: "/reactHello",
    component: () => {
        return (
            <Suspense fallback={<div>Loading...</div>}>
                <Hello />
            </Suspense>
        );
    }
},
{
    path: "/vuetest1",
    component: () => {
        return (
            <Suspense fallback={<div>Loading...</div>}>
                <div>
                    <h1>我是一个vue组件</h1>
                    <TestVue />
                </div>
            </Suspense>
        );
    }
}]
````  

## lazyReactInVue
在Vue的router里懒加载React组件
````js
import Vue from 'vue'
import VueRouter from 'vue-router'
import { lazyReactInVue } from 'vuereact-combined'
Vue.use(VueRouter)

const routes = [
  {
    path: '/',
    name: 'home',
    component: () => import('../views/Home')
  },
  {
    path: '/reactInVueDemo',
    name: 'reactInVueDemo',
    component: lazyReactInVue(() => import('../reactComponents/cc.jsx'))
  }
]

const router = new VueRouter({
  routes
})

export default router
````  

## withVueRouter
在react组件中获取vue router对象，可以通过props属性获取倒$vueRouter和$vueRoute
```jsx harmony
import React from 'react'
import { withVueRouter } from 'vuereact-combined'
class Test2 extends React.Component {
  constructor (props) {
    super(props)
  }
  componentWillMount () {

  }
  componentDidMount () {
    // 可以通过props属性获取倒$vueRouter和$vueRoute
    console.log(this.props.$vueRouter, this.props.$vueRoute)
  }

  render () {
    return (
      <div>
        test2
        <h3>{this.props.$vueRoute.query.b}</h3>
      </div>
    )
  }
}
export default withVueRouter(Test2)
```  

## applyReactRouterInVue
建议在react项目的app或者main引入，然后再任何一个被转换的vue组件中都可以直接获取到实例属性$reactRouter,其中包含了react router的history、location、match
#### app.jsx
```jsx harmony
import { applyReactRouterInVue } from 'vuereact-combined'
import { withRouter } from 'react-router-dom'
applyReactRouterInVue(withRouter)
```  
#### demo.vue
```vue
<template>
    <div>
      <h1>demo</h1>
      <h2>{{$reactRouter.location.search}}</h2>
    </div>
</template>

<script>
export default {
  mounted () {
  }
}
</script>
```  
## 需要注意的包囊性问题  
由于在每一次跨越一个框架进行组件引用时，都会出现一层包囊，这个包囊是默认是以div呈现，并且会被特殊属性标注
React->Vue，会在vue组件的dom元素外包囊一层标识data-use-vue-component-wrap的div
Vue->React，会在react组件的dom元素外包囊一层标识__use_react_component_wrap的div
如果引发样式问题，可以对applyVueInReact、applyReactInVue方法传入第二个参数`options`  
```jsx
import VueComponent from './VueComponent.vue'
import { applyVueInReact } from 'vuereact-combined'
const VueComponentInReact = applyVueInReact(VueComponent, {
  react: {
    // react.componentWrapAttrs代表是vue组件在react组件中的组件包囊层的标签设置
    // 以下设置将设置组件的包囊层div的display为inline-block
    componentWrapAttrs: {
      style: {
            display: 'inline-block'
        },
      class: 'react-wrap-vue-component-1'
    },
    // react.slotWrapAttrs代表是vue组件在react组件中的插槽包囊层的标签设置
    // 以下设置将设置插槽的包囊层div的display为inline-block
    slotWrapAttrs: {
      style: {
          display: 'inline-block'
      }
    },
  },
})
```
以下是默认配置
```jsx
// 默认配置
const originOptions = {
    react: {
        componentWrap: 'div',
        slotWrap: 'div',
        componentWrapAttrs: {
            __use_react_component_wrap: '',
            style: {
                all: 'unset'
            }
        },
        slotWrapAttrs: {
            __use_react_slot_wrap: '',
            style: {
                all: 'unset'
            }
        }
    },
    vue: {
        // 组件wrapper
        componentWrapHOC: (VueComponentMountAt, nativeProps = []) => {
            // 传入portals
            return function ({ portals = [] } = {}) {
                return (<div {...nativeProps}>{VueComponentMountAt}{portals.map((Portal, index) => <Portal key={index}/>)}</div>)
            }
        },
        componentWrapAttrs: {
            'data-use-vue-component-wrap': '',
            style: {
                all: 'unset',
            }
        },
        slotWrapAttrs: {
            'data-use-vue-slot-wrap': '',
            style: {
                all: 'unset'
            }
        }
    }
}
```  
## 対応レベル  
#### react componentsにvue componentsを導入  
特徴｜サポート｜説明  
-|-|-  
通常属性 | 完全対応 |  |  
html フラグメント属性 | 方向転換したサポート | vueの名前付slotを使って、$slotで取得する。 | 
render props | 方向転換したサポート | vueのScoped Slotを使って，$scopedSlotsで取得する |  
children(通常のスロット) | 完全対応 |  |  
コンポーネントの合成イベント | 完全対応 | on属性によって。 |  
コンポーネントのネイティブイベント(.native) | 未対応 | reactは、自らをカプセル化することができる。 |  
v-model | 方向転換したサポート | model を渡し、vue コンポーネントのv-model プロパティの任意のカスタマイズをサポートします。 |   
html snippetsでreactやvueのコンポーネントを使用す | 完全対応 | reactコンポーネントは直接渡すことができ、vueコンポーネントはapplyVueInReactで変換されます。 |  
vueコンポーネントをlazyロードする | 完全対応 | lazyVueInReactを使用 |  
redux | 完全対応 | applyReduxを使用 |  
mobx | 方向転換したサポート | mobxはreactとvueを独自の方法で接続している |  
vuex | 完全対応 | applyVuexを使用 |  
sync修飾子 | 方向転換したサポート | $syncを使用 |  
イベント修飾子(key.enter、click.once) | 未対応 | 自己処理 |  
トランスミッション | 方向転換したサポート | data-passed-propsを使用 |  
ref | 方向転換したサポート | refはまずカプセルインスタンスを返し、カプセルインスタンスのvueRef属性は反転したVueコンポーネントインスタンスを取得する |  
react router(vueコンポーネントの中) | 完全対応 | applyReactRouterInVueを使用 |  
変換されたかどうかの判定 | 完全対応 | props属性 data-passed-props または instance 属性 reactWrapperRef を介して。 |  

#### vueコンポーネントにreactコンポーネントを導入する  
特徴｜サポート｜説明  
-|-|-  
通常属性 | 完全対応 |  |  
名前付きslot | 完全対応 | reactのプロパティを利用 | 
Scoped slot | 完全対応 | reactでpropertyを使って取得、typeは関数 |  
通常のslot | 完全対応 |  |  
コンポーネントの合成イベント | 完全対応 | reactのプロパティを利用 |  
コンポーネントのネイティブイベント(.native) | 現時点では未対応 |  |  
v-model | 未対応 | reactコンポーネントにはこの概念がない |  
provider/inject | 現時点では未対応 | 将来的にサポートする予定 |  
sync修飾子 | 未対応 | reactコンポーネントにはこの概念がない |  
redux | 完全対応 | applyReduxを使用する |  
mobx | 方向転換したサポート | mobxはreactとvueを独自の方法で接続している |  
vuex | 完全対応 | applyVuexを使用する |  
イベント修飾子(key.enter、click.once) | 未対応 | reactコンポーネントにはこの概念がない |  
reactコンポーネントのlazyローディング | 完全対応 | lazyReactInVueを使用 |  
トランスミッション | 方向転換したサポート | data-passed-propsを使用 |  
ref | 方向転換したサポート | refはまずカプセルインスタンスを返し、カプセルインスタンスのproperty reactRefはその逆のリアクトコンポーネントインスタンスを取得します。 |  
vue router(reactコンポーネントの中) | 完全対応 | withVueRouterを使用 |  
変換されたかどうかの判定 | 完全対応 | props 属性 data-passed-props またはインスタンス属性 vueWrapperRef を使用 |    
