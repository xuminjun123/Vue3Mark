# Vue-Router

## 1、安装

npm install vue-router@next



~~~javascript
--cdn
https://unpkg.com/vue-router@3.0.0/dist/vue-router.js
~~~



-- 使用

~~~javascript
import { createRouter ,createWebHashHistory} from 'vue-router'

import Dashboard from './Dashboard.vue'
import Todos from './Todos.vue'
const router = createRouter({
    history:createWebHashHistory(),
    routes:[
        {path:'/',component:Dashboard},
        {path:'/todos',component:Todos},
        
    ]
})
createApp(App)
.use(router)
.mount('#app')

~~~

## 2、新特性



一 、动态路由的添加

之前Vue2.x动态添加路由是多个的，现在也可以动态添加单个路由

~~~javascript
// 新特性 
// 一 、动态路由的添加
router.addRoute({
    path:'/about',
    name:'about',
    component:()=>import('./components/About')
})

// 可以指定父级
--main.js
router.addRoute('about',{    // 父级为about
    path:'/about/info',
    component:{
        render(){
            return h('div','info page')
        }
    }
})

-- About.vue
<template>
  <div>
      about page
      <router-view></router-view>
  </div>
</template>
~~~





二、动态路由的跳转、守卫

~~~vue

<template>
  <div>
      <button @click="nav">跳转</button>
  </div>
</template>
<script>
import {useRouter} from 'vue-router'
// 获取路由实例
const router = useRouter()

// route是响应式对象，可以监控变化
const route = useRoute();
watch(
      ()=>route.query,
      (query)=>{
    		console.log(query)
		}
     );

// 路由守卫 -- 离开
onBeforeRouteLeave((to,from)=>{
    const answer =window.confirm('你确定离开当前页面吗？')
    if(!answer){
        return false
    }
})    
    
    
setup(){
	return {
		nav(){
			router.push('/')
		}
	}
}
</script>    
~~~



三、NavLink 



**暂时不懂，留着****

~~~javascript
~~~



## 3、vue-router变化01

1. 路由　通配符 ＊ 被移出

~~~javascript
-- 路由　通配符 ＊ 被移出
const router = createRouter({
    history:createWebHashHistory(),
    routes:[
       {
           path:"/:pathMatch(.*)*",  // 表示路由没找到
           name:"not-found",
           component: NotFound,
       }    
    ]
})
router.resolve({
    name:"not-found",
    params:{
        pathMatch:['not','found']
    }
})

~~~

2.  路由　通配符 ＊ 被移出

~~~javascript
-- isReady() 替代onReady()
 // router.push()
 // router.onReady(onSuccess,onError)
router.isReady().then(onSuccess).catch(onEorror)
~~~



3. scrollBehavior 变化

~~~javascript
-- v2.x
scrollBehavior(to,from,savedPosition){
    // 以前 {x:0,y:0} 被移出
    // 现在 {left:0,top:0}
    if(savedPosition){ // 如果之前有保存的位置
        return savedPosition   // 回到之前的位置
    }else{
        return {top:0}
    }
}
~~~

   

   

   ##　4、vue-router变化０２［重要］　

keepalive和transition 必須用在router-view 内部

~~~vue
-- vue2.x写法
<keep-alive>
    <router-view></router-view>
</keep-alive>

-- vue3写法
<router-view v-slot="{Component}">
	<keep-alive>
        <组件 :is="Component"></组件>
    </keep-alive>
</router-view>
~~~





**router-link 移除了一票属性**

vue2.x 中 <router-link>组件的属性有：

to 、replace、 append、 tag、 active-class、 exact 、 event、 exact-active-class

全部移除



- append

~~~vue
-- vue2.x
<router-link to="child" append></router-link>

-- vue3
<router-link :to="append($route.path ,'child' )"></router-link>
~~~



- tag/event

~~~vue
<router-link to="/xx" tag="span" event="dblclick"></router-link>


<router-link to="/xx" custom v-slot="{navigate}">
    <span @dblclick="navigate">xxx</span>
</router-link>
~~~



- exact 精确匹配 

~~~vue
~~~



##　6、路由移除的api

1. mixins中的路由守卫 将被忽略

官方还未解决



2. match 方法被移除，使用resolve替代

3. 移除router.getMatchedComponents（）

4. 包括首屏导航在内的所有导航均为异步

~~~vue
app.use(router)
router.isReady().then(()=>app.mount('#app'))
~~~

> 如果首屏存在路由守卫，则可以不等待就绪直接挂载，产生结果将和Vue2相同



5. route的parent 属性移除

~~~vue
const parent = this.$route.matched[this.$route.matched.length - 2]
~~~



6. 解析路径的第三方库  path-to-regexp 

- pathToRegexpOotions选项被移除 --------> strict

- caseSensitive 被移除 --------> sensitive

~~~vue
createRouter({
	strict: boolean,
	sensitive:boolean
})
~~~



7. 使用 history.state 【 不懂 】



## 8. 路由API

1. routes 选项是必填项

~~~vue
createRouter({routes:[]})
~~~



2. 跳转不存在命名路由报错

~~~vue
router.push({name:'dashboad'})
~~~



3. 修改必填参会抛出异常
4. 命名子路由，如果 path 为空时候不在追加 /

~~~vue
[
	path:'/dashboard',
	children:[
		{path:'',component:DashboardDefault}
	]
]
~~~

 在Vue2中生成url会自动追加 " / "



5. $route 属性编码行为

params、query、hash 如果这些属性需要编码的 ，比如有 " + " 等特殊符号

- Path、pullpatch 不再做编码
- hash 会被解码
- push 、resolve和replace ，字符串参数或者对象参数Path属性必须编码
- params / 会被解码
- query 中 + 不处理 ，stringifyQuery









#　Vuex4 快速上手

vue4 是Vue3 的兼容版本，关注于兼容性 。

提供和Vue3相同的ＡＰＩ，可以直接复用vuex 

## 安裝 vuex4

~~~vue
npm i vuex@next
~~~



## 初始化

Vuex 4 初始化做了相應的變化

~~~javascript
createApp(App)
	.use(router)
	.use(store)
~~~



~~~javascript
import { createdApp } from 'vue'
import { createStore } from 'vuex'
import App from './App.vue'

// 1. 创建store 实例
export const store = createStore({
    state(){
        return {
            count:1
        }
    },
    mutations:{
        add(state){
            state.count ++;
        }
    }
})
~~~

--- 使用



~~~vue
-- vue2 传统写法
<p @click="$store.commit('add')">
    {{$store.state.count}}
</p>

--vue3 composition 写法
<p @click="add">
    {{count}}
</p>
~~~

vue3 setup

~~~javascript
import {userStore} from 'vuex'
import {toRefs} from 'vue'

setup(){
	const store = useStore();
	console.log(store)
	return {
		// count: store.state.count,
        ...toRefs(store.state),
		add(){
			store.commit('add')
		}
	}
}

~~~





#  使用TS 编写VueX

~~~javascript
import { ComponentsCustomProperties } from "vue";
import { store } from "vuex";

// delcare your own store states
export interface State {
	counter: number;
}    

declare module "@vue/runtime-core" {
    // provide typing for `this.$store`
    interface  ComponentCustomProperties {
        $store: Store<State>
    }
}
~~~



userStore() 类型化

~~~javascript
setup 中使用useStore时 要类型化，共需要三步

1. 定义`InjectionKey`
2. app 安装时提供`InjectionKey`
3. 传递`InjectionKey` 给`userStore`
~~~



