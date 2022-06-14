

# 1. 项目名称: 



**`基于vue的网易严选移动端项目`**



# 2. 项目描述：

本项目是一款电商类型的移动端APP，采用Vue框架构建，并使用Vant组件库搭建页面，在美观效果上保证了用户体验，登录注册模块采用了本地存储技术。



# 3. 技术架构：

使用Vue CLi搭建vue项目

使用axios并二次封装拦截器发送请求

使用Vuex存储管理数据

使用Vue Router管理路由

使用Less预编译Css

使用Vant库中的组件搭建页面结构

使用Lodash上封装的方法



# 4. 技术难点：

### 4.1 引入并暴露

- 写api模块化引入并暴露，vuex，发送请求  这一流程中遇到bug，请求发不出去

> 引入并暴露，暴露出去的是一个对象，
>
> 该对象里装的是api文件夹下的index.js 文件中所有引入的模块化对象
>
> 
>
> 在vuex模块化的小store中：
>
> ```js
> 这样引入
> import {shopcart} from '@/api'
> reqShopCartList是一个对象
> ```
>
> 
>
> 在actions中：需要通过这个对象  `.`  它身上的方法
>
> ```js
> async getShopCartList({ commit }) {
> 	const result = await shopcart.reqShopCartList()
> 	console.log(result)
> }
> ```
>
> 
>
> 



### 4.2 重写路由器VueRouter原型上的push、replace方法



```js
VueRouter.prototype.push = function(location,resolved,rejected){
  if(resolved === undefined && rejected === undefined){
     代表用户使用的时候，没有传递成功或者失败的回调
    return originPush.call(this,location).catch(() => {})
  }else{
     代表用户至少传递了一个回调，用来处理promise
     只要用户传递了回调，无论成功还是失败，都是可以的。
    return originPush.call(this,location,resolved,rejected)
  }
}
```



### 4.3 localStorage本地存储token

- 现象：在发送获取购物车列表的请求时，发现拿到的是空数组数据

- 原因：获取购物车列表需要携带用户token

- 解决：

  > 因此需要写一个登录请求，拿到token存储在localStorage中
  > 在utils里的请求拦截器中去添加请求头携带token信息
  > `config.headers.token = localStorage.getItem('token_key')`

  

### 4.4 使用`QRcode`生成微信支付二维码

```js
async generateQR(text) {
    try {
        const result = await QRCode.toDataURL(text);
        this.urlCode = result;
    } catch (err) {
        console.error(err);
    }
},
```



### 4.5 支付轮询

```js
在弹出二维码后，需要开启定时器发送请求 **轮回**`查询支付订单状态`

因为在支付成功后，还需要进行关闭dialog组件、关闭定时器、弹出“支付成功！” 的Toast轻提示 的一些操作

所以需要在actions中 return返回 await等待的请求成功返回的promise对象的值 给到 组件中用result接收

判断 result.code 若为 200 则清空定时器clearInterval(this.timeoutID)，并将timer置为null、关闭dialog、轻提示（支付成功）

```









# 5. 整体项目问题:  （至少写100字）



### 5.1 在点击切换单个商品选中状态时出现bug：

有时候改变成功，有时候发现闪烁一下已改变又跳回改变前的状态

```js
分析这种情况的不稳定可能是异步的原因！

得知在点击按钮的函数里要发两次请求：
1、切换单个商品选中状态
2、获取购物车列表
且应该有先后顺序

因此加上 async await 让异步代码同步阻塞化即可解决
```



### 





