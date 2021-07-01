# 实战

## 创建项目

 `yarn create @vitejs/app`





**setup语法糖**

~~~javascript
<script setup>
import { defineProps, reactive } from 'vue'

defineProps({
  msg: String
})

const state = reactive({ count: 0 })
</script>
~~~



==setup核心思想：==

- 简化一个 组件的声明过程 （ import 导入, template  中可以直接使用 ）
- 无需要return  {  ref , reactive 内的数据 }



## vite2 主要变化

- 配置选项变化 : `vue 特有选项` 、创建选项 、css 选项 、jsx选项
- 别名行为优化:  不在要求`/`开头或结尾
- `Vue支持` ： 通过 @Vitejs/plugin-vue 插件支持

- React 支持
- HMR API 变化
- 清单格式变化
- 插件API 重新设计



## vue3 setup详解

1. 直接导入组件

~~~javascript
import { defineProps, reactive } from 'vue'
~~~

2. 属性定义

~~~javascript
defineProps({
  msg: String
})
~~~

3. 定义事件

~~~javascript
- 子组件emit 事件

<!--
	第一种写法 ：
    <button @click="emit('myclick')">emit</button> 
-->
<!-- 第二种写法 -->
<button @click="onclick">emit</button>


// 定义事件
const emit = defineEmit(['myclick'])
const onclick = () => {
  emit('myclick')
}


- 父组件接收
<HelloWorld msg="Hello Vue 3 + Vite" @myclick="onmyclick" />

import HelloWorld from 'comps/HelloWorld.vue'
const onmyclick = () => {
  console.log('myclick from hello world');
}    
~~~



emit 事件还可以 使用上下文

~~~javascript
// 获取上下文
const  ctx = useContext()
console.log(ctx); // attrs emit expose  slots....

// 定义事件
const emit = defineEmit(['myclick'])
const onclick = () => {
  // emit('myclick')
  ctx.emit('myclick')
}
~~~

4. 对外暴露事件

~~~javascript
- 子组件
// 获取上下文
const ctx = useContext()
console.log(ctx); // attrs emit expose  slots....
// expose 可以对外暴露 事件
ctx.expose({
  someMethod() {
    console.log('some message from HelloWorld');
  }
})

- 父组件
<HelloWorld msg="Hello Vue 3 + Vite" @myclick="onmyclick" ref="hw" />

import HelloWorld from 'comps/HelloWorld.vue'

import { ref } from "vue";
const hw  = ref(null);
 
const onmyclick = () => {
  console.log('myclick from hello world');
  hw.value.someMethod();
}    
~~~



## Vue jsx 支持

 这个插件只对 单文件支持

~~~vue
npm i @vitejs/plugin-vue-jsx
~~~



vite.config.js 配置

~~~javascript

import vueJsx from "@vitejs/plugin-vue-jsx"

// https://vitejs.dev/config/
export default defineConfig({
  plugins: [vueJsx()]
})

~~~



使用

~~~vue
<!--
 * @Description: jsx - vue Test
 * @Author: xmj
 * @Date: 2021-06-30 15:37:43
-->
<script lang="jsx">
import { ref } from "vue";
export default {
    setup() {
        const counter = ref(0);
        const onclick = () => {
            counter.value++
        }

        return () => (
            <div>
                <p>comp</p>
                <button onClick={onclick}> {counter.value} </button>
            </div>
        )
    }
}
</script>
~~~





## mock 数据

~~~javascript
npm i vite-plugin-mock cross-env -D

npm i mockjs -S
~~~



vite.config.js 配置

~~~javascript

import vueJsx from "@vitejs/plugin-vue-jsx"

// https://vitejs.dev/config/
export default defineConfig({
  plugins: [viteMockServe({ supportTs: false })]
})
~~~



# router/vuex

## router安装 

~~~javascript
npm i vue-router@next vuex@next -S
~~~

~~~javascript
- router/index.js

import { createRouter, createWebHashHistory,createWebHistory} from 'vue-router'

export const routes = [
   {
        path: '/', 
       	component: () => import('views/home/index.vue'),
       	name: 'home',
    },
]


const router = createRouter({
    history: createWebHistory(),
    routes
});
export default router;
~~~



-main.js

~~~javascript
import { createApp } from 'vue'
import App from './App.vue'
import router from './router'

createApp(App).use(router).mount('#app');
~~~



 -app.vue

<router-view />



##　vuex

－store/index.js

 ~~~javascript
 import { createStore} from 'vuex';
 
 export default store = createStore({
     state: {
         counter: 0,
     },
     add(state){
         state.counter++;
     }
 });
 ~~~

-main.js

~~~javascript
// vuex
import store  from './store';

createApp(App).use(router).use(store).mount('#app');
~~~



使用

~~~javasc
<p @click="$store.commit('add')">{{$store.state.counter}}</p>
~~~



# 整合element3

-下载 sass

~~~javascript
npm i sass -D
~~~



-全局引入 element3

~~~javascript
// 全局引入element3
import element3 from 'element3';
import 'element3/lib/theme-chalk/index.css';

createApp(App)
    .use(router)
    .use(store)
    .use(element3)
    .mount('#app');
~~~

