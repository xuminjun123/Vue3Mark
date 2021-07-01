# vite 

 ## vite 特点

1. 冷启动 。 冷启动： 开发预览中是不会进行打包的
2. 开发可以实现热更新
3. 按需进行编译 ，不会刷新全部的DOM节点 





##  vite.config.js

~~~javascript
// 取别名
const { resolve } = require('path')

export default {
    alias: { '/@/': resolve(__dirname),'src'}
}

// 使用
import xxx from "/@/components/xxx.vue"
~~~

