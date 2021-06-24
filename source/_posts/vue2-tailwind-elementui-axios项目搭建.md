---
title: vue2+tailwind+elementui+axios项目搭建
image: /images/vue.jpg
date: 2021-06-02 23:32:20
tags: [vue,javascript]
categories: 
- 前端
---

## 安装

### nodejs&npm

> nodejs是javescript代码运行的基础
>
> npm是nodejs的包管理管理工具，前端开发用到的第三方包都可以用它来安装

安装可以参考 **[安装Node.js和npm - 廖雪峰的官方网站 (liaoxuefeng.com)](https://www.liaoxuefeng.com/wiki/1022910821149312/1023025597810528)**

运行如下两条命令如果看到正确的输出结果，证明已经安装成功

```
node -v
npm -v
```

### vue2

> vue是尤雨溪大神写的一个前端框架，可以快速的完成前端开发
>
> 虽然vue3已经发布了，但是新的版本可能有些第三方组件没有适配，出现问题也不好查到，建议还是使用二代

使用npm安装vue2

```cmd
npm install vue-cli -g
```

验证vue2是否安装成功: 输出版本即可证明安装成功  （**这里的版本是vue-cli的版本**）

```cmd
vue -V
```



## 创建项目

### 创建vue2项目

```cmd
vue init webpack my_vue
```

接下来会要求输入一些创建项目的选项

- ？ Project name（my_vue）  

  > 项目名。  直接敲回车使用默认名字， 或者自己输入项目名

- ？Project Description

  > 项目描述。  

- ？ Author

  > 项目作者

- ?  Vue build

  > 敲回车，选择默认选项

- ？Install vue-router

  > 是否安装vue-router.  这是vue的路由管理组件，输入Y安装

- ? Use EsLint to lint yourcode

  > EsLint是用来使用ES6的语法标准来规范代码。  
  > 如果自己的代码很不规范的话，建议使用。  
  >
  > 个人不建议使用

- ？ Set up unit tests

  > 是否设置单元测试。 一般不选，输入n

- Setup e2e tests with Nightwatch?   

  > 不选

- Should we run `npm install` for you after the project has been created? (recommended)

  > 项目创建后是否使用npm install 进行组件的安装，  选择是

最后等待一会儿，项目就创建好啦！

命令行cd到项目目录； 

运行npm run dev，可以看到项目运行的url

打开浏览器输入该url，就可以看到项目的初始样子了：

![](/images/image-20210525132241052.png)

### 项目目录说明

- build  

  > 项目构建的配置，一般不用动

- config

  > 项目的一些配置。
  >
  > **特别注意：**如果在开发过程中需要修改项目运行的ip或者端口号，可以修改config/index.js中的配置
  > ![](/images/image-20210525132646932.png)

- node_modules

  > 用到的组件库，不用管

- **src  工程主目录**

  - assets

    > 项目用到的静态资源，比如图片、css文件等等

  - components

    > vue组件，可以理解为一个个页面。初始会有一个HelloWorld.vue，也就是我们刚开始在浏览器中见到的页面
    >
    > **页面不一定要放到该目录中，可以自己新建文件夹**

  - router

    > vue的路由信息

  - App.vue

    > 项目的入口。
    >
    > 简单解释一下：我们写好网站之后，进入网站，就相当于进入了一个App，类似一个安卓或者苹果应用
    >
    > 定义了一些样式，在写项目的时候一般要修改掉

  - main.js  **重要**

    > 定义了项目的入口
    >
    > 项目用到的组件需要在此注册
    >
    > 后续会多次用到main.js

- static

  > 静态资源目录
  >
  > 比如网站在浏览器页签显示的角标，可以放在这里面

### 开发工具

​	推荐使用webstorm.  安装和破解方法在此不再赘述。

​	当然很多人也喜欢vscode，安装、配置使用可以自行百度。



## 组件的安装与使用

### vue-router的使用

> vue-router定义网站的路由跳转信息。 下面以一个案例来说明vue-router的使用
>
> 假设我们的ip 和端口分别是 localhost:8080

#### 简单路由实现

1. 创建新页面
   在components中创建几个页面，即 .vue文件：

   - First.vue
   - Second.vue

   修改文件内容，用来标识文件

   ```vue
   <template>
     <div>					<!--最外层一定要有一个容器，一般用div-->
       <h1>First页面</h1>  <!--second修改这里就好-->
     </div>
   </template>
   
   <script>
   export default {
     name: "Second"
   }
   </script>
   
   <style scoped>
   
   </style>
   
   ```

   

2. 修改配置router/index.js

   > 想要在浏览器中看到我们新建的页面，就要在路由中定义相关页面的路由

   先看最初始的路由内容：

   ```js
   import Vue from 'vue'
   import Router from 'vue-router'
   import HelloWorld from '@/components/HelloWorld' //引用HelloWorld页面
   
   Vue.use(Router)
   
   export default new Router({
     routes: [
       {
         path: '/',     			// 页面路径，即'Ip:端口/' 
         name: 'HelloWorld',		// 路由名字，必须唯一
         component: HelloWorld  	// 路径对应的页面
         // 下面这种写法，也能够引用页面，不需要在上面引用，注意后面的.vue后缀不能省略
         //component: () => import("../components/HelloWorld.vue"),
       },
       // 新的路由信息在这个地方添加
     ]
   })  
   ```

   加上新写的两个页面路由后，变成如下：

   ```js
     //mode: 'history'，   // 加入这一行可以取消url中的 # 号 
     routes: [
       {
         path: '/',
         name: 'HelloWorld',
         component: HelloWorld
       },
       {
         path:'/first',
         name: 'First',
         component: () => import("../components/First.vue"),  // first页面
         meta: {title:'First'}   // 这里是定义路由的元信息，属于自定义内容
       },
       {
         path:'/second',
         name: 'Second',
         component: () => import("../components/Second.vue"), // second页面
         meta: {title:'First'}
       },
     ]
   ```

   url说明：加入mode: 'history'这一行可以取消url中的#号，参考[ vue-router 路由模式及url中#号的解析](https://blog.csdn.net/qq_34911465/article/details/79405796)

   也就是是说，加了这一行，你的url就是：

   - http://localhost:8080/first
   - http://localhost:8080/second

   不加的话，就是：

   - http://localhost:8080/#/first
   - http://localhost:8080/#/second

   现在npm run dev启动项目，查看这两个，发现多了一个vue的logo。前面提到过，App.vue中的内容会影响其他所有页面，因此我们对App.vue进行修改

3. 修改App.vue
   主要修改两个地方

  - img  删掉
  - margin-top的样式 改为0 或者删掉

  现在重新打开页面查看，发现已经ok啦！

4. 兄弟路由

   > 兄弟路由即两个同级别的路由，两个页面不会互相嵌套

   上面的first second页面就是两个兄弟页面

   

#### 父子路由

> 父子路由也可以称之为嵌套路由. 在网站中非常常见。
>
> 下面演示用法

- 创建两个‘儿子’页面 Son1.vue  和Son2.vue，内容如下

  ```vue
  <template>
    <div>
      <h1>子页面1</h1>  <!--Son2.vue 这里改成 子页面2-->
    </div>
  </template>
  
  <script>
  export default {
    name: "Son1"
  }
  </script>
  
  <style scoped>
  
  </style>
  ```

- 创建父组件 Father.vue

  ```vue
  <template>
    <div>
      <div style="text-align: center">   <!-- 简单设置样式为居中 -->
        <router-link to="/son1">
          <button>前往子页面1</button>
        </router-link>
        <router-link to="/son2">
          <button>前往子页面2</button>
        </router-link>
      </div>
  
      <div>
        <router-view></router-view>
      </div>
    </div>
  </template>
  
  <script>
  export default {
    name: "Father"
  }
  </script>
  
  <style scoped>
  
  </style>
  
  ```

  - router-link   

    > 此标签用来进行路由跳转， to关键字定义要跳转的路由路径，只需要填写ip:port后面的路径
    >
    > 在浏览器点击标签内的内容就可以前往相应的页面

  - router-view

    > 该标签定义了一个槽位，子页面将会嵌套到这里

- 路由定义

  ```js
  ...
   {
        path:'/father',
        name: 'Father',
        component: () => import("../components/Father.vue"),
        meta: {title:'父页面'},
        children: [
          {
            path:'/son1',
            name: 'Son1',
            component: () => import("../components/Son1.vue"),
            meta: {title:'子页面1'}
          },
          {
            path:'/son2',
            name: 'Son2',
            component: () => import("../components/Son2.vue"),
            meta: {title:'子页面2'}
          },
        ]
      },
  ...
  ```

  注意子页面的路由信息是放在父页面的children属性下的，这是父子组件必须有的路由结构

- 现在打开url   http://localhost:8080/father  点击两个按钮是不是可以切换子页面啦？  并且父组件定义的按钮还在页面上，证明嵌套成功！

#### 路由守卫

> 在进行路由跳转的时候，有时要对路由进行判断，比如根据跳转修改页面的标题，身份验证、或者错误的路由导入重定向到指定的页面等等
>
> 下面演示一下路由守卫是如何修改页面标题的

修改main.js 如下：

```js
import Vue from 'vue'
import App from './App'
import router from './router'

Vue.config.productionTip = false

// 这里是添加的路由守卫
router.beforeEach((to,from,next)=>{
  document.title = `${to.meta.title}`;
  next()
})

/* eslint-disable no-new */
new Vue({
  el: '#app',
  router,
  components: { App },
  template: '<App/>'
})
```

beforeEach在每次路由跳转前会执行，（同样也有afterEach，在每次路由跳转后执行）。 参数说明

- to： 跳转前的路由对象，也就是我们在router/index.js中定义的路由信息。  
  加入我们从son1页面跳转到son2页面，那么to的内容就是：

  ```js
   {
       path:'/son1',
       name: 'Son1',
       component: () => import("../components/Son1.vue"),
       meta: {title:'子页面1'}
   }
  ```

- from: 跳转后的路由对象

- next()  因为beforeEach相当于在路由跳转过程中插入了一段代码，因此必须调用next()才能继续执行跳转

这里我们用了我们自定义的路由信息来修改页面的标题。 打开浏览器，测试两个子页面的切换，发现已经成功修改了页面标题

### axios

> 前端主要是用来展示的，但是没有数据展示什么呢？ 这就需要网络模块了。
>
> axios是一个封装的很好的HTTP库，有了它，我们就可以使用http协议向后端请求数据，或者传输数据

#### 安装

```cmd
npm install axios --save   // 朴实无华的安装命令，注意要在项目目录下，也就是src同级目录下
```

#### 封装

> 想要更傻瓜式的使用axios，我们需要进行简单的封装，此处查看注释了解各处的功能即可

我们在src下新建文件夹http

创建request.js文件，写入如下内容

```js
import axios from "axios";

const instance = axios.create({
  baseURL : "https://v1.alapi.cn",  // 因为我们没有后端，这里写一个第三方免费接口的地址
  timeout : 10000

})

instance.defaults.headers.post['Content-Type'] = 'application/json;charset=UTF-8'; // poset请求的头部信息

// 请求拦截器
instance.interceptors.request.use(
  config => {
    // 每次发送请求之前判断vuex中是否存在token
    // 如果存在，则统一在http请求的header都加上token，这样后台根据token判断你的登录情况
    // 即使本地存在token，也有可能token是过期的，所以在响应拦截器中要对返回状态进行判断
    // const token = store.state.token;
    // (config.headers.Authorization = token);
    // console.log(config)
    return config;
  },
  error => {
    return Promise.error(error);
  })


// 响应拦截器
instance.interceptors.response.use(
  response => {
    // 如果返回的状态码为200，说明接口请求成功，可以正常拿到数据
    // 否则的话抛出错误
    console.log(response)
    if (response.status === 200) {
      return Promise.resolve(response);
    } else {
      return Promise.reject(response);
    }
  },
  // 服务器状态码不是2开头的的情况
  // 这里可以跟你们的后台开发人员协商好统一的错误状态码
  // 然后根据返回的状态码进行一些操作，例如登录过期提示，错误提示等等
  // 下面列举几个常见的操作，其他需求可自行扩展
  error => {
    if (error.response.status) {
      return Promise.reject(error.response);
    }
  },
)


/**
 * get方法，对应get请求
 * @param {String} url [请求的url地址]
 * @param {Object} params [请求时携带的参数]
 */
export function get(url, params){
  return new Promise((resolve, reject) =>{
    instance.get(url, {
      params: params
    }).then(res => {
      resolve(res.data);
    }).catch(err =>{
      reject(err.data)
    })
  });}

/**
 * post方法，对应post请求
 * @param {String} url [请求的url地址]
 * @param {Object} params [请求时携带的参数]
 */
export function post(url, params) {
  return new Promise((resolve, reject) => {
    instance.post(url, params)
      .then(res => {
        resolve(res.data);
      })
      .catch(err =>{
        reject(err.data)
      })
  });
}
```

在同目录下创建api.js

将后端的接口变成前端的一个个方法，然后就可以在vue页面组件中引用这些方法

```js
import {get,post} from "./request";

// 第三方的免费接口，用来知乎日报
export const GetZhihuDaily = (param)=>get("/api/zhihu/latest",param)
```

#### 使用

我们在son1页面中演示一下上面这个接口的使用

先看修改后的文件，再来一一说明：

```vue
<template>
  <div>
    <h1>子页面1</h1>

    <div v-for="item in myData">  <!--7.渲染api获取的数据到页面中-> 
      <h4>{{item.title}}</h4>
    </div>
  </div>
</template>

<script>
import {GetZhihuDaily} from "../http/api";  // 1.引用请求方法

export default {
  name: "Son1",
  data(){ // 组件中的数据都会存在data中
    return{
      myData:[],   // 2.定义一个数组用来保存我们请求的来的数据
    }
  },

  created() { // 每次重新打开页面时，created中的代码会执行
    this.FetchData()  // 6.请求数据
  },

  methods:{ // 方法必须放在method中，包括网络请求，点击事件等等
    FetchData(){    // 3. 定义一个新方法，在方法中发送网络请求
      GetZhihuDaily().then(res=>{ // 4. 调用网络请求方法
        console.log(res)
        if(res.code===200){ // 判断请求是否成功
          this.myData = res.data.stories  // 5.将网络请求来的数据保存在mydata中，这里的stories是这个api数据特有的
        }
      })
    }
  }
}
</script>

<style scoped>

</style>

```

具体步骤已经写在了代码注释中，下面对几个步骤再做进一步的解释

1. 没什么好说的，要用就要引入，否则报错
2. 这是vue的规则，数据要写在data函数中，{}括号包含的就是当前组件拥有的变量，可以使用 **this.变量名** 获取
3. methods也是vue的规则，在其中定义新方法，同样通过 **this.方法名** 调用
4. 调用了我们的网络请求方法。 因为axios网络请求是**异步**的，因此要在then中定义方法，用来处理请求成功后的事件，这里我们使用了箭头函数，res代表的就是网络请求返回的数据，我们首先判断请求是否成功，然后把数据赋值给我们的变量
5. 略
6. 定义了方法后，还需要调用，可以通过点击事件、也可以通过生命周期函数。  created就是vue的一个周期函数([详解vue生命周期 - SegmentFault 思否](https://segmentfault.com/a/1190000011381906)),会在每次重新打开页面时执行。
7. 因为我们的数据是一个数组，所以选择v-for（[vue2之v-for详解](https://blog.csdn.net/amyleeYMY/article/details/84940020)）来遍历渲染数据

最后打开页面，可以看到数据已经成功获取并渲染到页面上啦： （如果没有获取到的话，请F12 - network . 刷新页面看看网络请求成功发送）

![](/images/image-20210527104748364.png)

### element-ui

> 经过上面的操作步骤，我们已经成功获得数据并渲染到了页面上。 但一个大问题，我们的页面太丑了。 前端一般使用css来修改样式的，但是css比较复杂，学习成本也比较高。 我们需要傻瓜式的ui组件。 好在很多开源的工具我们都可以拿来用，比如Vuetify 、 Vue Material、Element UI、Buefy 、Quasar 、Bootstrap Vue 等等。  我们这里以[ Element](https://element.eleme.cn/#/zh-CN/component/installation)来举例，它是由饿了么前端团队开发的UI组件库

#### 安装

```cmd
npm install element-ui --save
```

#### 配置

在main.js中引用并使用配置elmentui

```js
import ElementUI from 'element-ui'            // 引用组件库
import 'element-ui/lib/theme-chalk/index.css'  //引用样式

Vue.use(ElementUI) // 在vue中注册使用组件库
```

#### 使用

在father中，将第一个按钮修改一下。 即把button改成el-button

```html
<el-button>前往子页面1</el-button>
```

打开页面，可以看到按钮变成了elementui定义的样式：

![](/images/image-20210527115242944.png)

当然，还有很多elementui组件的样式和功能，请参照官方文档进行使用

### tailwindcss

> 有时发现elementui的样子并不是我们想要的，并且很难修改。想要自己写样式，但又懒得学css。 这时就可以使用tailwindcss，它封装了非常多的样式，可以直接以class的形式定义组件的样式

#### 安装  

>  参考[Vue 2 安裝 Tailwind CSS 1 | 點燈坊 (fpjs.fun)](https://fpjs.fun/tailwind/general/vue2-tailwind1/)
>
>  注意vue2必须指定如下版本，否则用不了

1. 安装

```npm
npm install tailwindcss@1.9.5
```

2. 在src/assets中创建css/tailwind.css, 写入如下内容

```css
@tailwind base;
@tailwind components;
@tailwind utilities;
```

3. 运行如下命令

   ```cmd
   npx tailwindcss init
   ```

   在项目目录下生成了一个tailwind.config.js文件

4. 修改tailwind.config.js

   ```js
   module.exports = {
     future: {
       // removeDeprecatedGapUtilities: true,
       // purgeLayersByDefault: true,
     },
     purge: [
       './src/**/*.html',
       './src/**/*.vue',
     ],
     theme: {
       extend: {},
     },
     variants: {},
     plugins: [],
   }
   ```

5. 在项目目录下创建**postcss.config.js** 文件，内容如下

   ```js
   module.exports = {
     plugins: [
       require('tailwindcss')
     ]
   }
   ```

6. 修改根目录下的.postcssrc.js 为如下内容

   ```js
   module.exports = {
     "plugins":[
       require('tailwindcss')
     ]
   }
   ```

#### 配置

在main.js中配置使用tailwindcss

```js
import '@/assets/css/tailwind.css'
```

#### 使用

再次修改Father.vue,这次修改另一个按钮为如下：

```html
<button class="bg-pink-500 text-white px-4 py-4">前往子页面2</button>
```

class中定义了4个内容

- bg-pink-500  定义背景色为pink-500
- text-white  定义字体颜色为白色
- px-4  定义左右内边距为4
- py-2  定义上下内边距为2
- shadow-md   定义按钮的阴影
- rounded-2xl  定义按钮的圆角

效果如下

![](/images/image-20210527122422292.png)

更多丰富的用法请参考[中文文档 - Tailwind CSS 中文文档](https://www.tailwindcss.cn/docs)

### vuex

> 这是vue的全局状态组件，也就是全局变量组件。 在项目中可能存在一些全局变量，使用vuex就可以方便的获取、同步修改、异步修改这些变量

#### 安装

安装命令：(--save的作用是将组件安装到当前项目中，而不是全局。 下同)

```
npm install vuex --save
```

在项目目录/src下新建文件夹/store，并在文件夹下创建多个文件

- store.js  vuex的主要程序
- states.js  在这里定义全局变量
- getters.js  在这里定义全局变量的获取方法
- mutations  在这里定义全局变量的同步操作方法
- actions 在这里定义全局变量的异步操作方法

#### 使用

1. 首先在states中定义一个全局变量

   ```js
   const state =  {
       count:0
   }
   export default state
   ```

2. 在getters中定义获取count变量的方法

   ```
   const getters =  {
       docount:(state,getters) => {
           return state.count
       }
   }
   export default getters
   ```

3. 在mutations中添加如下内容

   ```js
   const mutations = {
       setCount(state, v) {
           state.count = v;
       },
       addCount(state,v) {
           if(v){
               state.count += v
           }else{
               state.count ++
           }
       },
       reduceCount(state,v) {
           if(v){
               state.count -= v
           }else{
               state.count --
           }
   
       }
   }
   export default mutations
   ```

4. 修改actions内容,  可以看到其中可以加入异步内容，我们这里没有使用

   ```js
   const actions = {
       ADD_COUNT(vuex,num) {
           // axios.get("/customer/user_info").then(res => {
           //     commit(TYPES.SET_COUMT, res.data);
           // });
           vuex.commit("addCount", num);
       },
       REDUCE_COUNT(vuex,num) {
           // axios.get("/customer/user_info").then(res => {
           //     commit(TYPES.SET_COUMT, res.data);
           // });
           vuex.commit("reduceCount", num);
       }
   }
   export default actions
   ```

5. 在store中引入以上组件

   ```js
   import Vue from 'vue'
   import Vuex from 'vuex'
   
   import state from "./states";
   import getters from "./getters";
   import mutations from "./mutations";
   import actions from "./actions";
   
   Vue.use(Vuex)
   
   export default new Vuex.Store({
     state,
     getters,
     mutations,
     actions,
   })
   ```

6. 修改main.js引入vuex

   ```js
   import store from "./store/store";  // 引入vuex
   
   /* eslint-disable no-new */
   new Vue({
     el: '#app',
     router,
     store,   // 加入这一行
     components: { App },
     template: '<App/>'
   })
   ```

#### 演示

下面演示我们如何在网页中使用定义的count全局变量

方案： 在Father中获取并展示count变量； 在子页面1中执行count+1，在子页面2中执行count-1

1. 修改Father.vue
   获取count值

   ```js
   computed:{   // computed中的函数必须有返回值，vuex会实时更新这个值
       myCount(){
         console.log(this.$store.getters.docount)
         return this.$store.getters.docount
       }
   }
   ```

   展示count值

   ```html
       <div class="bg-blue-400 py-2 text-4xl text-white mx-auto my-2">
         {{myCount}}
       </div>
   ```

2. 修改Son1.vue
   添加一个方法，用来增加count的值

   ```js
       // 修改vuex中的变量值
       AddCount(){
         this.$store.dispatch('ADD_COUNT')
       }
   ```

   添加一个按钮用于触发这个方法

   ```html
    <el-button type="primary" @click="AddCount">add count</el-button>
   ```

3. 修改Son2.vue
   添加一个方法，用来增加count的值

   ```js
    // 修改vuex中的变量值
       AddCount(){
         this.$store.dispatch('ADD_COUNT')
       }
   ```

   添加一个按钮用于触发这个方法

   ```html
   <el-button type="danger" @click="ReduceCount">reduce count</el-button>
   ```

现在可以打开网页测试一下啦

![](/images/image-20210527201535270.png)

## 运行

```
npm run dev
```

## 打包

```cmd
npm run build
```

打包后，项目目录下会出现一个dist目录，这个就是我们打包的结果



## nginx部署

> 前端编译后的结果最后还是要部署到服务器上才行，我们打包得到dist后，如何去访问网站呢？ 直接打开dist/index.html可以看到并不能打开我们的网站。 还是需要部署在nginx服务器才行。

### nginx安装

- [win10 上 安装 nginx - 简书 (jianshu.com)](https://www.jianshu.com/p/d2f30962e8ce)
- [(3条消息) LINUX安装nginx详细步骤_大蛇王的博客-CSDN博客_linux安装nginx](https://blog.csdn.net/t8116189520/article/details/81909574)

### nginx配置

nginx配置是很重要的一步。

以win10为例，在nginx安装目录下找到conf/nginx.conf，这就是我们要配置的文件

我们在http{}中添加如下内容

```conf
    server {
        listen       8888;					# 访问端口
        server_name  你的ip:8888;		# 因为没有域名，所以这里是你的ip：端口

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location / {
            root   D:\app\my_vue\dist; 		# 指向我们打包后的目录
            index  index.html index.htm;	

            try_files $uri $uri/ /index.html;  # 没有这一行，可能会报404错误
        }

        #error_page  404              /404.html;

        # redirect server error pages to the static page /50x.html
        #
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
    }
```



启动nginx

```shell
start nginx.exe 
```

打开浏览器  http://你的ip:8888/father

成功！

## 总结

以上的内容只是我个人的前端开发经验，有些步骤也许并不标准，但应该也是有用的。

虽然尽可能写的详细点，还是可能有疏漏。 欢迎交流。

最后的页面那么丑，主要是本人不擅长设计，这也不是本文的主要目的，见谅。

另外关于vue的学习资料（都是我用过的）：

视频教程

- https://www.bilibili.com/video/BV12J411m7MG   

资料

- [rumengkai/awesome-vue: Vue相关开源项目库汇总 (github.com)](https://github.com/rumengkai/awesome-vue)

- [kon9chunkit/GitHub-Chinese-Top-Charts: GitHub中文排行榜，帮助你发现高分优秀中文项目、更高效地吸收国人的优秀经验成果；榜单每周更新一次，敬请关注！](https://github.com/kon9chunkit/GitHub-Chinese-Top-Charts#Vue)



