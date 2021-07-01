# Vue3.0



## 1. reactive简单使用

`reactive`

~~~vue
<template>
  <p>{{data.counter}}</p>
</template>

<script>
import { reactive } from 'vue'  
export default {
  name: 'HelloWorld',
  setup(){
    const data = reactive({
      counter:1
    })
    return {data}
  }
}
</script>
~~~



如果需要使用计算属性`computed`

~~~vue
<template>
  <p>{{data.counter}}</p>
  <p>{{data.doubleCounter}}</p>
</template>

<script>
import { computed, reactive } from 'vue'  
export default {
  name: 'HelloWorld',
  setup(){
    const data = reactive({
      counter:1,
      doubleCounter: computed(()=>data.counter * 2)
    })
    return {data}
  }
}
</script>
~~~

 

加入生命周期 ` onMounted, onUnmounted`

~~~Vue
<template>
  <p>{{data.counter}}</p>
  <p>{{data.doubleCounter}}</p>
</template>

<script>
import { computed, onMounted, onUnmounted, reactive } from 'vue'  
export default {
  name: 'HelloWorld',
  setup(){
    const data = reactive({
      counter:1,
      doubleCounter: computed(()=>data.counter * 2)
    })

    let timer;
    // 生命周期
    // 初始化完成  
    onMounted(()=>{
      timer = setInterval(()=>{
        data.counter++
      },1000)
    })

    // 移除
    onUnmounted(()=>{
      clearInterval(timer)
    })
    return {data}
  }
}
</script>
~~~



## ref简单使用

~~~vue
<template>
  <p>{{msg2}}</p>
</template>

<script>
import {ref} from 'vue'  
export default {
  name: 'HelloWorld',
  setup(){

    // ref
    const msg2 = ref('some msg2')

    return {data ,msg2 }
  }
}
</script>

~~~



##　小优化

如果setup 内容過多，可以适当的提取

~~~vue
<template>
  <p>{{msg2}}</p>
  <p>{{data.counter}}</p>
  <p>{{data.doubleCounter}}</p>
</template>

<script>
import { computed, onMounted, onUnmounted, reactive ,ref} from 'vue'  
export default {
  name: 'HelloWorld',
  setup(){
    // 引入  
    const data = useCounter()
    // ref
    const msg2 = ref('some msg2')

    return {data ,msg2 }
  }
}

// 从setup 中提取出来    
function useCounter(){
  const data = reactive({
      counter:1,
      doubleCounter: computed(()=>data.counter * 2)
    })

    let timer;
    // 生命周期
    onMounted(()=>{
      timer = setInterval(()=>{
        data.counter++
      },1000)
    })

    // 移除
    onUnmounted(()=>{
      clearInterval(timer)
    })
    return data;
}
</script>
~~~



 如果嫌 每次都要写 data,那么使用 toRefs转为但一对象

~~~Vue
<template>
  <p>{{counter}}</p>
  <p>{{doubleCounter}}</p>
</template>

<script>
import { computed, onMounted, onUnmounted, reactive , toRefs} from 'vue'  
export default {
  name: 'HelloWorld',
  setup(){
    // 使用了toRefs 才可以{ } 解构
    const {counter, doubleCounter} = useCounter()

    return {counter, doubleCounter  }
  }
}

function useCounter(){
  const data = reactive({
      counter:1,
      doubleCounter: computed(()=>data.counter * 2)
    })

    let timer;
    onMounted(()=>{
      timer = setInterval(()=>{
        data.counter++
      },1000)
    })

    onUnmounted(()=>{
      clearInterval(timer)
    })

    // 使用 toRefs 转为ref
    return toRefs(data);
}
</script>
~~~



## 2 . 使用 侦听器watch 和元素ref的引用

~~~vue
<template>
  <p>{{counter}}</p>
  <p>{{doubleCounter}}</p>
  <p ref="desc"></p>
</template>

<script>
import { computed, onMounted, onUnmounted, reactive, toRefs, watch} from 'vue'  
export default {
  name: 'HelloWorld',
  setup(){
    // 使用了toRefs 才可以{ } 结构
    const {counter, doubleCounter} = useCounter()

    // 使用元素引用
    const desc = ref(null);
    // 侦听器
    watch(counter,(val,oldVal)=>{
     	const p = desc.value
     	p.textContent = `counter change from ${oldVal} to ${val}`  
    })

    return {counter, doubleCounter ,msg2 , desc}
  }
}

function useCounter(){
  const data = reactive({
      counter:1,
      doubleCounter: computed(()=>data.counter * 2)
    })

    let timer;
    // 生命周期
    onMounted(()=>{
      timer = setInterval(()=>{
        data.counter++
      },1000)
    })

    // 移除
    onUnmounted(()=>{
      clearInterval(timer)
    })

    // 使用 toRefs 转为ref
    return toRefs(data);
}
</script>

~~~



## 3. 传送门Teleport

传送门组件提供一种简洁的方式可以指定它里面的内容 的父元素（  弹窗组件 ）

`Teleport` 使用

components / ModalButton

~~~vue
<template>
    <button @click="modalOpen = true">弹出一个全屏窗口</button>
{{modalOpen}}
    <!--  teleport 系统内部全局组件 -->
    <teleport to="body">
        <!-- 传送的就是 div -->
        <div v-if="modalOpen" class="modal">
            <div>
                这是一个模态窗口
                我的父元素是"body"!
                <button @click="modalOpen = false">Close</button>
            </div>
        </div>
    </teleport>
</template>

<script>
import { reactive,toRefs } from 'vue'
export default {
    setup() {
        const data = reactive({
            modalOpen:false
        })

        return  toRefs(data)

    }
}
</script>

<style scoped>
.modal {
    position: absolute;
    top: 0;
    right: 0;
    bottom: 0;
    left: 0;
    background-color: rgba(0, 0, 0, 0.5);
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
}
.modal div {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    background-color: white;
    width: 300px;
    height: 300px;
    padding: 5px;
}
</style>
~~~



使用

~~~vue
<template>
  <!-- Teleport: ModalButton -->
  <modal-button></modal-button>
</template>

<script>
import ModalButton from './ModalButton.vue'
export default {
  name: 'HelloWorld',
  components:{
    ModalButton
  },
}
</script>
~~~



## 4. Fragments 和Emits 选项

Vue3中组件发送的自定义事件需要定义在emits 选项中

- 原生时间会触发两次 ， 比如click
- 更好的指示组件工作方式

- 对象形式事件校验



`emits`

~~~vue
<template>
  <!-- 最好可以不要和原生事件重名  -->
  <div @click="$emit('click')">
    <h3>自定义事件</h3>
  </div>
</template>

<script>
export default {
    emits:['click']  //  使用emits就不会除非原生事件
}
</script>
~~~

使用

~~~vue
<template>
  <!-- Emits 选项 -->
  <emits @click="onClick"></emits>
</template>

<script>
import Emits from './emits.vue'
export default {
  name: 'HelloWorld',
  components:{
    ModalButton,
    Emits
  },
  methods:{
    onClick(){
      console.log('clicl me!');
    }
  },
}
</script>

~~~



## 5. 自定义渲染器 custom render

Vue3.0 中支持 自定义渲染器（Renderer） ：

这个ＡＰＩ可以用来自定义渲染逻辑。比如下面的案例我们可以把书记渲染到canvas上



首先创建一个组件描述要渲染的数据 ， 我们想要渲染一个叫做piechar的组件，我们不需要单独声明该组件，因为我们指向把它写到的数据绘制到canvas 上， 创建CanvasApp.vue

~~~javascript
import { createApp, createRenderer } from 'vue'
import App from './App.vue'
import './index.css'
import CanvasApp from './CanvasApp.vue'
// createApp(App).mount('#app')

// createRenderer 自定义渲染器
createRenderer(App).mount('#app')

const nodeOps = {
    createElement(tag, isSVG, is) {
        // 处理元素创建逻辑
        return { tag }
    },
    insert(child, parent, anchor) {
        //处理元素插入逻辑
        // 1. 如果是真实DOM元素，在这里会是canvas,需要绘制
        //    
    },
    remove: child => { },
    createText: text => { },
    createComment: text => { },
    setText: (node, text) => { },
    setElementText: (el, text) => { },
    parentNode: node => { },
    nextSibiling: node => { },
    querySelector: selector => { },
    setScopeId(el, id) { },
    cloneNode(el) { },
    insertStaticContent(content, parent, anchor, isSVG) { },
    patchProp(el, key, prevValue, nextValue) {
        // 属性更新
        el[key] = nextValue
    }
}
const renderer = createRenderer(nodeOps);
const app = renderer.createApp(CanvasApp)
// 扩展mount :收I西安创建一个画布元素
function createCanvasApp(App){
    const APP= renderer.createApp(App);
    const mount = app.mount
    app.mount = function(selector){
        
    }
}
~~~















## 6. Global API 改为应用程序实例调用



Vue2中有很多全局API可以改变Vue的行为，比如`Vue.component` 等。

这会导致一些问题 ：

1. Vue2没有App概念，new Vue() 得到的根实例被作为app，这样的化所有创建的根实例是共享相同的全局配置，这在测试是会污染其他测试用例，导致测试变得困难
2. 全局配置也导致没有办法在单页面创建不同全局配置的多个app 实例



Vue3 中使用createApp返回app实例，由他暴露一系列全局api。



~~~javascript
-- main.js
createApp(App)
.component('comp',{
    render(){
        return h('div','I an comp')
    }
})
.mount('#app')

-- 使用
<comp />
~~~



![vueCompApi](D:\typora\Vue3.0\Vue3Img\vueCompApi.png)



## 7.Global and internal APIs 重构为可做摇树优化

Vue2 中不少global-API 是作为金泰函数直接挂在构造函数上的，列如 Vue.nextTick（）,

如果我们从位子代码中用过它们，就会形成所谓的`dead code` ,这类global-API 造成的`dead code` 无法使用webpack的tree-shaking 排除调



vue2.x 

~~~vue
import vue from 'vue';
Vue.nextTick(()=>{
	// ...
})
~~~



vue3 中做的响应的变化，将他们抽取称为独立函数，这样打包工具的摇树优化可以将这些dead code 排除掉。

~~~vue
import {  nextTick } from 'vue';
nextTick(()=>{
	// ...
})
~~~



受 影响的 API  ：

1. `Vue.nextTick`
2. `Vue.observable(replaced by vye.reactive)`

3. `Vue.version`
4. `Vue.compile`

5. `Vue.set`

6. `Vue.delete`



##  8.v-model变化

父组件

~~~vue
<template>
  <!-- v-model -->
  <vmodel-test v-model:counters="counters"></vmodel-test>
  <!-- 等价 -->
  <vmodel-test :counters="counters" @update:counters="counters=$event">	 </vmodel-test>
</template>

<script>
import VmodelTest from './VmodelTest.vue'
export default {
  name: 'HelloWorld',
  components:{
    VmodelTest
  },
  data(){
    return {
      counters:1
    }
  },
}
</script>
~~~





子组件

VmodelTest.vue

~~~vue
<template>
    <div @click="$emit('update:counters',counters + 1)">
        counter：{{counters}}
    </div>
</template>

<script>
export default {
    props: {
        counters: {
            type: Number,
            default: 0 
        },
    },
}
</script>
~~~



## 9. 渲染函数API的变换



~~~vue
<template>
  <!-- render api 发生变化 -->
  <RenderTest v-model:counter="counter"></RenderTest>
</template>

<script>
import { h } from 'vue'
export default {
  name: 'HelloWorld',
  components: {
    
    RenderTest: {
      props:{
        counter:{
          type:Number,  
          default:0
        }
      },
      render() {
        const emit = this.$emit;
        const counter = this.counter;
        return h('div',[
          h('div',{onClick(){
            emit('update:counter',counter + 1)
          }},`I am RenderTest:${counter}`)
        ])
      }
    }
  },
}
</script>
~~~

或者 把methods写到

~~~vue
<template>
<!-- render api 发生变化 -->
<RenderTest v-model:counter="counter"></RenderTest>
</template>

<script>
    import { h } from 'vue'
    export default {
        name: 'HelloWorld',
        components: {
            RenderTest: {
                props:{
                    counter:{
                        type:Number,
                        default:0
                    }
                },
                render() {
                    return h('div',[
                        h('div',{onClick: this.onClick},`I am 									RenderTest:${this.counter}`)
                    ])
                },
                methods: {
                    onClick() {
                        this.$emit('update:counter',this.counter + 1)
                    }
                },
            }
        },
    }
</script>
~~~



## 10.  加入slot



~~~vue
<template>
 
  <!-- render api 发生变化 -->
  <RenderTest v-model:counter="counter">
    <template v-slot:default>xxx</template>
    <template v-slot:content>content...xxx</template>
  </RenderTest>
</template>

<script>
import { h } from 'vue'
export default {
  name: 'HelloWorld',
  components: {
    ModalButton,
    Emits,
    VmodelTest,
    RenderTest: {
      props:{
        counter:{
          type:Number,
          default:0
        }
      },
      render() {
        // 得到slots里面的内容
        // this.$scopedSlots.content()   vue2.x中的写法,vue3中被废除
        return h('div',[
          h('div',{onClick: this.onClick},
          `I am RenderTest:${this.counter}`,
          this.$slots.content(), // vue3 写法
          this.$slots.default()
          ),
        ])
      },
      methods: {
        onClick() {
          this.$emit('update:counter',this.counter + 1)
    	 }
	  },
    }
  },
}
</script>

~~~



## 11. 函数式组件使用变化

函数式组件变化较大，  主要有几点 ：

1. 性能提升在Vue3中可以忽略不计，所以Vue3中推荐使用状态组件
2. 函数式组件仅能通过纯韩寒诉形式声明，接受props和context 两个参数
3. SFC中<template> 不能添加function 特性声明函数式组件
4. { function: true } 组件选项移除



声明一个函数式组件 ，Functional.vue

Functional.vue

~~~vue
<script>
    import { h } from 'vue'
    // vue3中函数式组件只能使用函数方式声明
    function Heading(props,context){
        console.log(context);

        return h(`h${props.level}`,context.attrs,context.slots)
    }
    Heading.props = ['level']
    export default Heading
</script>
~~~

-- 使用

~~~VUE 
<template>
  <Functional level="3">这是一个动态h元素</Functional>
</template>

<script>
    import Functional from './Functional.vue'
    export default {
        name: 'HelloWorld',
        components: {
            Functional,
        }
    },
</script>
~~~



## 12. 异步组件发生的变化

由于Vue3中函数式组件必须为纯函数 异步组件定义时有以下变化 ：

1. 必须明确使用 defineAsyncComponent 包裹
2. `component`选项重名为`loader`
3. Loader 函数不再接受 `resolve` and `rejiect`且必须返回一个Promise



定义一个异步组件

~~~vue

<!-- 异步组件 -->
<AsyncComp></AsyncComp>

import { defineAsyncComponen } from 'vue'
components: {
    AsynvComp: defineAsyncComponent( ()=> import('./NextPage.vue') ),
}
~~~



带配置的组件，loader选项十一前的component

~~~javascript
import ErrorComponent from './components/ErrorComponent.vue'
import LoadingComponent from './components/LoadingComponent.vue'

// 带配置的异步组建
const anyncPageWithoptions = defineAsyncComponent({
    loader:()=> import('./NextPage.vue')
    delay:200,
    timeout:3000,
    errorComponent:ErrorComponent,
    loadingComponent: LoadingComponent
})
~~~



## 13. 组件data 选项应该总是声明为函数，组件白名单

~~~javascript
createApp({
    data(){
        return {
            apiKey: 'a1'
        }
    }
}).mount("#app")
~~~



==自定义组件白名单==

Vue3 中自定义元素检测发生在模板编译时，如果要添加一些Vue之外的自定义元素，需要在编译器选项中设置

`isCustomElement` 选项。

使用构建工具时，模板都会用Vue-loader 预编译，设置它提供的	`compilerOptions`  即可

~~~javascript
rules :[
    {
        test:/\.vue$/,
        use:'vue-loader',
        options:{
            compilerOptions:{
                isCustomElement:tag => tag === 'plastic-button'
            }
        }
    }
    // ...
]
~~~

使用的Vite ，在Vite.config.js 配置`vueCompilerOptions`即可 ： vite.config.js

~~~javascript
module.exports ={
    vueCompilerOptions: {
        isCustomElement : tag => tag === "piechart"   //  piechart是组件名称 
    }
}
~~~



## 14.动态組件

vue3中  is只能使用在 component 上

~~~vue
<component is="comp"></component>
~~~



`v-is` 指令 ， 呃呃 ，不太懂 先留着

##  15. 自定义指令directive

Vue3中自定义指令api 和组件保持一致 （功能相同，就是换了一个名字）

- bind ———> **beforeMount**

- inserted ———> **mounted**

- **beforeUpdate**: new ! 元素自身更新前调用和组件生命周期钩子很像
- update（ 被移除 ）  ———> **removed** 和updated基本相同 ,因此被移出 ，使用uodated 代替
- componentUpdated ———> **updated**

- beforeUnmount : new! 和組件生命周期钩子相似，元素将要被移除之前调用
- unbind ———> **unmounted**



实验 ：

~~~javascript
createdApp(App)
	.component('comp',{
    	render(){
            return h('div','I am comp')
        }
	})
	.directive('highlight',{
    	beforeMount(el,binding,vnode){
            el.style.background = bind.value
        }
	})
	.mount("#app")
~~~



使用 v-highlight=" 'green' " 



## 关于动画transition 类名变更



~~~vue
-- html
  <!-- transition  动画-->
  <transition name="fade">
    <p>hello</p>
  </transition>

-- css
  .fade-enter-active,
  .fade-leave-active {
    transition: opacity 0.5s ease;
  }
  /* 进入起始点，结束点 */
  .fade-enter-from,  
  .fade-leave-to {
    opacity: 0;
  }
~~~



## 16. 事件修饰符api,filter等被移除



~~~vue
 <!-- keyCode 被移出-->
 <input v-on:keyup.13="submit"  />

 <!-- 只能使用alias 方式 -->
 <input v-on:keyup.enter="submit" />
~~~



==$on.$off and $once 移除==

上述3个方法被认为不应该有Vue 提供，一次被移出，可以使用其他第三方库实现 。

## 17. 组件之间通信



~~~javascript
-- 组件之间的通信
// npm i mitt -S
<script src="https://unpkg.com/mitt/dist/mitt.umd.js"></script>
~~~



~~~javascript
-- 使用
- 组件1 发送事件
<button @click="sendMsg">emit event</button>

import mitt from 'mitt'
// 创建emitter 
export const emitter = mitt()

// 发送事件
methods: {
    sendMsg(){
      emitter.emit('someEvent','参数')
    }
  },

-- 组件2 监听事件      
// 监听事件
import { emitter } from './HelloWorld.vue'
 mounted(){
     emitter.on('someEvent',msg =>{
         console.log('msg==>',msg);

     })
}
~~~



==Filters 移除==

Vue3中移除过滤器，请调用方法或者计算属性代替



## 18. 实战案例

















 
