[TOC]

# 版本问题

```json
"vue": "^2.6.14",
"vue-router": "^3.5.3",//vue2的路由用的是3版本
"vuex": "^3.6.2",//vue2的vuex用的是3版本
"axios": "^0.26.1",
"less": "^4.1.2",
"less-loader": "^7.3.0",
"pubsub-js": "^1.9.4",
"@vue/cli-service": "~5.0.0",
"eslint": "^7.32.0",
"dayjs": "^1.11.0",
"nanoid": "^3.3.2",
```





------



# 关闭eslint语法检查

> 在vue.config.js里配置
>
> ```js
> lintOnSave: false, //关闭eslint语法检查
> ```



------



# 配置代理（配置看门狗，让看门狗生效）

> 在vue.config.js里配置
>
> ```js
> devServer: {
>     proxy: {
>       "/api": {
>         target: "http://localhost:3000", //代表转发的目标服务器
>         pathRewrite: { "^/api": "" }
>         //路径重写
>         // 什么时候重写？ 看最终服务器的接口身上有没有
>       }
>     }
>   }
> ```



------

# jsconfig.json配置别名@提示

> ```js
> {
>   "compilerOptions": {
>     "baseUrl": "./",
>     "paths": {
>         "@/*": ["src/*"]
>     }
>   },
>   "exclude": ["node_modules", "dist"]
> }
> ```



------

# Router/index.js报错

> ```js
> [Vue warn]: Error in render: "TypeError: Cannot read properties of undefined (reading 'matched')"
> 
> TypeError: Cannot read properties of undefined (reading 'matched')
> ```
>



问题出在：在router文件夹下的index.js里

> ```js
> //原来的代码
> export const router = new VueRouter({
>  base: '/',
>  mode: 'history',
>  routes: [
>      {
>          path: '/home',
>          component: Home
>      }
>  ]
> });
> ```
>
> ```js
> //更改后即可
> export default new VueRouter({
>  base: '/',
>  mode: 'history',
>  routes: [
>      {
>          path: '/home',
>          component: Home
>      }
>  ]
> });
> ```
>
> 



# 解决编程式导航多次点击参数不变抛出的NavigationDuplicated异常（二次封装）

vue_router3.1.0版本以上引入了promise的语法：

若传递的参数没有改变，多次点击该路由路径跳转，会抛出错误：

```js
Uncaught (in promise) NavigationDuplicated: Avoided redundant navigation to current location
```



**原因是**：内部的promise是失败的，而我们没有进行处理所造成的（promise成功可以不处理，失败必须处理）



**解决：**

> 1：降版本	【弊端：不好】
> 2：处理失败的promise对象  在push调用后加.catch(()=>{}) 	【弊端：每次调用都要加】
> 3：修改源码（二次封装） 【优点：一劳永逸】



修改原型身上的push 和 replace

this.$router.push(),这个push方法其实是VueRouter的prototype里面的一个方法

我们现在需要把这个方法改造一下:



#### push的二次封装：

1、保存原来的push方法，以备后期使用

> ```js
> let originPush = VueRouter.prototype.push;
> ```
>



2、修改原型身上的push方法，改为我们自己定义的方法

> ```js
> //push的二次封装
> VueRouter.prototype.push = function(location,resolved,rejected){
> 	if(resolved === undefined && rejected === undefined){
> 		// 代表用户使用的时候，没有传递成功或者失败的回调
> 		return originPush.call(this,location).catch(() => {})
> 	} else {
> 	// 代表用户至少传递了一个回调，用来处理promise
>         //只要用户传递了回调，无论成功还是失败，都是可以的
> 		return originPush.call(this,location,resolved,rejected)
> 	}
> }
> ```



#### replace的二次封装：

```js
//replace的二次封装
//1、保存原来的replace方法，以备后期使用
let originReplace = VueRouter.prototype.replace;
//2、修改原型身上的replace方法，改为我们自己定义的方法
VueRouter.prototype.replace = function(location,resolved,rejected){
	if(resolved === undefined && rejected === undefined){
 		return originReplace.call(this,location).catch(() => {})
	} else {
		return originReplace.call(this,location,resolved,rejected)
	}
}
```



------

# 中断promise链

返回pending状态的promise

```js
return new Promise(() => {});
```





------

# 思想

#### 排他思想

#### 计数器思想

#### 开关思想

#### 参考值思想

定义一个值去参考一个值

------

# 事件回调与事件对象

#### 事件回调

其实我们自己所写的函数，根本不是事件的回调，而最终完整写法如下 



```js
($event) => {
	test($event)
}
```

$event其实才是真正的回调的，第一个形参，不是我们定义的，而是内置好的，不能修改

在回调函数的内部，默认是在调用我们自己写的函数

回调一定是函数定义，绝对不可能是函数调用，如果是，那就不是回调



#### 事件对象

回调是我们定义的，我们没调用，最后还执行了，那么函数既然执行了，就一定有人调，事件的回调是浏览器内核调的（系统），浏览器在调用这个函数的时候会自动把这一次事件触发的所有相关信息封装为一个对象，传递给回调的第一个形参，传递的这个对象被称作事件对象

------

# 自定义属性添加标识

HTML5写法：

data-xxx = ""

自定义属性 xxx="{}"

------

# 拦截器

**做两件事：**

>    1、修改报文（配置项）
>
>    2、添加额外的功能



------

# 发请求

工具utils

api接口请求函数

vuex存储

三连环



------

# 优化typeNav数据ajax请求次数，改变请求的位置

之前我们是在typeNav组件内部dispatch去发送ajax请求，这样的话

因为typeNav是被多个页面公用的，所以每次切换到一个页面，这个组件都会重新创建  mounted都会执行

因此有几个页面公用了这个typeNav就会执行几次ajax请求

所以我们放到App里面就只用执行一次，因为数据一样，没必要多次请求



------

# dispatch发送请求从mounted（只能发送第一次，后续dispatch发送请求需要用watch监视路由变化）+watch配合使用，进阶为直接在watch里dispatch加上immediate配置项

**最初思路：**

```js
methods：{
    getSearchInfo() {
        this.$store.dispatch("getSearchInfo", this.searchParams);
    },
},
mounted() {
  	this.getSearchInfo();
},
watch: {
    $route: {
      handler() {
        this.getSearchInfo();
    },
  },
},
```



在mounted的这一次的搜索只针对第一次从home页跳转到search页的时候

后续非第一次的，需要依赖watch监视到路由对象的改变，再次dispatch发送请求，因为后期在search页再去搜索的时候，search组件并没有切换销毁，mouted就只能执行一次

**最终：**直接在watch里dispatch，加上immediate配置项

```js
watch: {
    $route: {
      immediate: true,
      handler() {
        this.getSearchInfo();
    },
  },
},
```



------



watch可以保证数据已经回来了，然后再去实例化

就算数据回来，去实例化也有可能不行，必须要再次保证页面结构完全形成

this.$nextTick是在页面最近一次更新之后，执行传递的回调函数

------

# 节流与防抖

#### 正常：

事件触发非常频繁，而且每一次的触发，回调函数都要去执行（如果时间很短，而回调函数内部有计算，那么很可能出现浏览器卡顿）

#### 节流：

在规定的间隔时间范围内不会重复触发回调，只有大于这个时间间隔才会触发回调，把频繁触发变为少量触发

#### 防抖：

前面的所有的触发都被取消，最后一次执行在规定的时间之后才会，也就是说如果连续快速的触发  只会执行一次



------

# 路由传参相关

指定params参数时可不可以用path和params配置的组合?（对象写法）

1. 不可以用path和params配置的组合, 只能用name和params配置的组合
2. query配置可以与path或name进行组合使用



------

# 一些问题：



### 阻止事件冒泡可以提高性能吗

- 可以。
- 事件冒泡是客观存在的，被不被监视它都存在，因此一个事件会向上冒泡给它的祖宗十八代，所以当结构够深的时候，阻止其冒泡传播是有利于性能的。
- 但是要考虑开发中以后会不会用到的问题

------



### 路由组件和非路由组件的区别：

- 非路由组件： 

> 不需要路径的改变去切换
>
> 定义：定义组件都是创建一个.vue文件,只是定义的文件夹不同，它是在components里面
>
> 注册：在要使用的组件当中（或者全局注册）
>
> 使用：在哪用在哪写组件标签，使用注册的组件标签本质是内存中多了个组件对象
>
> 生命周期：非路由组件不会销毁



- 路由组件：

> 需要路径的改变才能切换
>
> 定义：定义组件都是创建一个.vue文件,只是定义的文件夹不同，它是在pages或views里面
>
> 注册：在路由器当中配置路由的时候注册
>
> 使用：在固定的内置组件当中进行切换  <router-view />，使用声明式导航（router-link,router-view）和编程式导航来使用(push replace router-view)
>
> 生命周期：路由组件在切换的时候，会销毁重建（keep-alive）

------

### this.$bus.$emit与直接this.$emit







------









