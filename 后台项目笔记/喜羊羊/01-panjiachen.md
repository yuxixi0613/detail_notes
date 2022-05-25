[TOC]



# `panjiachen`模板

## 文件认知

### 配置文件

```js
.editconfig	vscode编辑器的配置		
```



```js
根据不同的环境配置request里baseUrl的不同的基础路径
.env.development
.env.production	
```



```js
.eslintignore	eslint语法检查忽略
.eslintrc.js	eslint的配置文件
```



```js
.travis.yml		
```



```js
babel.config.js		element-ui按需引入
```



```js
jest.config.js		测试配置
```



```js
jsconfig.json		配置 @符
```



```js
LICENSE		许可证协议，需要遵循
```



```js
postcss.config.js	

'autoprefixer':{}	配置厂商前缀
```



```js
vue.config.js		脚手架提供的，它不是webpack配置文件，但是它里面配置的东西是服务webpack的
```





### src

`icons`

```js
svg		矢量图	不失真		常用来做小logo
jpg		位图	放大失真
```



```js
index.js


webpack语法：
const req = require.context('./svg', false, /\.svg$/)
const requireAll = requireContext => requireContext.keys().map(requireContext)
requireAll(req)
```



`layout`

```js
登录后的布局主页，是一级路由组件，比较重要因此并没有放到views里
```



`styles`

```js
后台用的scss

index.scss 总合所有的公共样式

然后在main.js引入
import '@/styles/index.scss' // global css
```





`main.js`

```js
国际化，在不同的国家，显示不同的语言
import locale from 'element-ui/lib/locale/lang/en' // lang i18n
```





`permission.js`	写的路由守卫

```js
全局前置守卫



全局后置守卫
    关闭NProgress
    NProgress.done()
```



`settings.js`

```js
title: '谷粒商城后台管理',		页签标题


fixedHeader: true,			  头部固定


sidebarLogo: false			  侧边条Logo
```





# 静态

删掉不用的

4个组件

4个路由

`tabel.js`  api



login组件中：

初步改写data里的用户名和密码的验证

用户名长度不能小于5

密码长度不能小于6



# 第一次发请求：



## 1.工具  

###  utils文件夹

`request.js`  

axios二次封装



请求拦截器

进度条之前写在这，现在写在路由守卫里

```js
config.headers.token = getToken()

可以直接这样写
config.headers['X-Token'] = this.store.token
```



`auth.js`

本模板是从Cookies里取的token

现在我们做持久化存储是通过local Storage



```js
改写if条件20000是mock返回的，200是真正的服务器返回的
if(res.code !== 20000 && res.code !== 200)
```





## 2.api

`login`

`getInfo`

`logOut`





看接口文档

把mock的

只用第一个和最后一个接口地址

swagger是一个在线的接口文档



备用服务器：

```
http://139.198.152.148:8170/swagger-ui.html
```



## 3.三连环store

> 复习不使用vuex的写法
>
> 把API挂在VUE原型上
>
> 在组件中`this.$API.req...`调用



本模板用了vuex

接下来写三连环

直接写了一个总`getters.js`

最后在index里引入配置一个getters



user.js中：

```js
vuex的第六个核心概念
namespaced:true		开启命名空间
开启之后，所有的getters、mutations、actions在使用的时候，都要添加模块名
user/login，

```



```js
使用了模块化，但未开启命名空间
不能使用数组的形式，而使用对象的方式
...mapState({
    token: state => state.user.token
})

使用了模块化，也开启了命名空间，这样做的好处是：
1.不同模块中的函数可以同名
2.mapState和mapGetters就可以继续写数组了，如果写数组，第一名字要一致，第二要添加两个参数
...mapState('user',['token'])

```



```js
RESET_STATE:(STATE) => {
    state.token = ''
    state.name = ''
    state.avatar = ''
}
```



actions中

把.then  .catch 改为async、await



点击登录请求返回404



## vue.config.js配置代理解决登录404问题

可以在`Vue CLI`或者`webpack`查

`devServer.proxy`



```js
devServer: {
    port:port,
    open: trye,
    overlay:{
        warnings:false,
        errors: true
    }
    proxy:{
        '/dev-api':{
            target:'http://39.98.123.211',
         	// target: 'http://139.198.152.148:8170',	//备用服务器
            pathRewrite:{'^/dev-api'}
        }
    }
}
```



重启



layout中

修改下拉

改成中文的退出和主页



**侧边栏的出现是`panjiachen`根据配的路由遍历动态生成的**

重定向若配在children里会在侧边栏生成一个空白的路由



dashboard的报错

```js
name:"首页"

改为
name:Dashboard
```



name一定要写对，否则后期权限会错







修改`navbar`面包屑`breadcrumb`

`breadcrumb`是一个公共非路由组件

改meta中的tittle

















































