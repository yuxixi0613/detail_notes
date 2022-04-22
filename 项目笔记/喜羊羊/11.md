[TOC]



# 静态

Center

组件

配路由



点击查看订单



二级路由Center

```js
children：[
	{

}，
	{
    
}

]
```



我的订单MyOrder

团购订单GroupOrder







右侧内容html、css挪到MyOrder



左侧

我的订单---router-link to

团购订单---router-link to



一上来默认显示MyOrder需要重定向

```js
{
    path:'',
    redirect:'/myorder'
}
```



在F12找到路由被点击的动态cllass，

给router-link-active添加高亮样式

```css
dd{
    .router-link-active{
        color:hotpink
    }
}
```





# 请求获取订单列表

api

reqOrderInfo

看文档



在MyOrder组件中



发请求

mounted：

getOrderInfo



methods：

getOrderInfo



在data中造参数

page:1

limit:3



在data中初始化orderList、total

orderList:[]

total:0



请求成功

将result.data.records赋值给orderList

将result.data.total赋值给total



# 展示动态数据

在orders有两个tabel

即每个订单是用一个tabel展示的



v-for遍历orderList



thead中：

creatTime

outTradeNo



tbody中：

orderDetailList遍历给tr



干掉td的rowspan



img的src改为detail.imgUrl



skuName	名字

skuNum	数量



consignee	昵称

totalAmount  总金额



paymentWay

orderStatusName



v-if条件渲染第一行

合并单元格

```js
:rowspan="order.orderDetailList.length"
```





# 分页器

四个数据

```js
当前页:currentPage='page'

总条数:total='total'

每页的条数:pageSize='limit'

连续页:continueNo=5
```



一个事件

```js
changePageNo="changePageNo"
```



methods:

```js
changePageNo(page){
    this.page = page
    发请求
    getOrderInfo()
}
```





合并请求的函数和自定义事件的回调

改写到getOrderInfo,会有第一次没传参的问题

因此需要形参设置默认值 getOrderInfo(page = 1)



# 基本业务逻辑杀青！



# 杂

## 图片懒加载

### 图片懒加载特点说明

> (1)   还没有加载得到目标图片时, 先显示loading图片
>
> (2)   在<img>进入可视范围才加载请求目标图片



### 下载

```js
npm i vue-lazyload
```



### 引入

main.js



```js
import VueLazyLoad from 'vue-lazyload'
```



将`:src`改为`v-lazy=""`





# 下午

# 跳转

未登录，访问trade、pay、center跳转到登录页面

登录后会自动跳转前面想去而没到的页面

判断路径是不是以  `/trade`     `/pay`   `/center`  开通



携带query参数标识target想去的地方



把if语句改用或运算符



# 加入购物车成功页面 添加 路由独享守卫

```js

只有携带了skuNum和sessionStorage内部有skuInfo数据，才能看到i俺家购物车成功的页面
beforeEnter:(to,from,next) => {
    
}
```



# 或使用组件内守卫

效果最终和路由独享守卫一样，只是配置的先后时间不同







# 路由独享守卫：只有从特定页面才能去到特定页面

## 只有从购物车shopcart页面才能跳到交易trade页面





## 只有从交易页面才能跳到支付页面





## 只有从支付页面才能跳到支付成功页面





# 重要~：{{{{{路由懒加载}}}}}



> 路由懒加载  
> 	调用import函数把一次性打包的所有路由组件分开去打包加载  
>
> ```js
> 路由懒加载特点
> 	每个路由组件打包会打包成一个单独的文件
> 	第一次访问哪一个路由组件，再去加载哪一个打包好的文件
> 
> (1）	当打包构建应用时，JS包会变得非常大，影响页面加载。如果我们能把不同路由对应的组件分割成不同的代码块，
> 	然后当路由被访问的时候才加载对应组件，这样就更加高效了
> 
> (2)	本质就是Vue 的异步组件在路由组件上的应用
> 
> (3)	需要使用动态import语法, 也就是import()函数
> 
> (4)	import('模块路径'): webpack会对被引入的模块单独打包一个小文件
> 
> (5)     当第一次访问某个路径对应的组件时，此时才会调用import函数去加载对应的js打包文件
> ```



```js
import aaa from './aaa'
import bbb from './bbb'
import ccc from './ccc'
这样的引入方式，我们称作叫同步引入或静态易怒，它不能动态引入（什么时候用什么时候引）

示例：
1.它不能动态引入（什么时候用什么时候引）
function fn(){
    import aaa from './aaa'
}
fn()
这样的方式是错误的

2.这样的引入方式，会让webpack一次性把引入的所有组件都打包到我们的主文件，浏览器在访问的时候，加载主文件，就已经把所有的组件加载了

由于上述两个特点的存在，这样的写法后期很少用，会造成浏览器加载页面效率变低
```



```js
import()	这个东西我们称作动态引入或者异步引入，它返回的是promise
1.可以思想异步引入组件（异步组件），也叫动态引入组件，其实就是import函数引入的组件
2.如果采用import函数去引入组件：
    ⑴所有采用import('./aaa')引入的组件都会单独的打包成一个小文件，不会打包进入主文件
    ⑵当我们第一次在浏览器访问到这个组件的时候，此时才会调用import函数，真正的去加载对于的小文件
    ⑶
    
示例：
function fn(){
    import('./aaa').then((module) =>{
        module.a
        module.b
    }).catch(() =>{
        
    })
}
fn()

const Home = () => import('./aaa')

{
    path:'/home',
    注册组件的时候也可以是一个函数
    component:() => import('./aaa')
}

```





# 插件validate



创建plugins文件夹









# 上线



打包dist文件

购买服务器

配置服务器

打开端口



阿里云最新活动

突发性能型很坑

ECS共享型

地区	华东

操作系统	Linux内核

CentOS	7版本





重置密码

安全组打开端口

修改规则

打开80端口



一键放通 ：常用端口

22：SSH端口

80：HTTP端口

ICMP：邮箱端口

3306：MySQL端口

27017：MongoDb端口









通过xshell连接云服务器，练习Linux命令
