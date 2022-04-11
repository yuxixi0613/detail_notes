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



# 解决编程式导航多次点击参数不变抛出的NavigationDuplicated异常（修改源码）

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

其实我们自己所写的函数，根本不是时间的回调，而最终完整写法如下

```js
($event) => {
	test($event)
}
```



------

自定义属性 

自定义属性 xxx="{}"

------

阻止事件冒泡可以提高性能吗



------







