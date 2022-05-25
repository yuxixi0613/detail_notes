[TOC]



## 1. 

1. 后台项目：后台管理 数据的可视化操作
2. 后台项目 二次开发
3. 开发 生产 测试服务器




## 4. 模板文件的介绍

1. src	# 源代码 源文件(source)

   api	接口请求函数

   asset 打包的静态资源

   components	全局公用组件

   icons 图标	项目所有 svg icons

   ​	svg 矢量图	jpg、png 位图(内图失真，内容丰富的图片)

   layout	后续说

   router	路由	路由懒加载

   store	vuex

   styles	公共样式 后台用 scss 集中于 index.scss 下面初始化 

   ​	最后在 入口文件 main.js 中引入

   utils	工具函数 request.js	index.js 自动化

   view	路由组件 404 找不到就显示404

   App.vue	入口页面

   main.js	入口文件

   ​	GitHub 搜索 css reset normalize.css

   ​	ElementUI	完整引入

   ​	locale 国际化 显示相应的国家语言

   permission.js	放的当初写在路由里面的守卫

   setting.js	设置 网站的一些常规化的操作 页签标题

   ​	fixedHeader	固定列标题

   ​	sidebarLogo	侧边条Logo

2. 项目 协同开发

## 5. 

1. 超级管理员 admin 账号密码 存后台数据库
2. 后台管理 无注册新用户 工能
3. router index.js 配置路由
4. 样式 scss scoped 作用域
5. login



## PM.1. axios二次封装、书写api和三连环

1. 收集数据 View login data loginForm:{}

2. 按钮登录 el-button @click.native.prevent="handleLogin"

3. methods 中定义函数 handleLogin

   this.$store .dispatch("user/login", this.loginForm)

4. 需要修改 data 里面函数

   const validateUsername = (rule, value, callback) => {}

   if(value,length < 5){}

   callback(new Error("用户名长度不能小于5"))

   1

5. 第一次发请求  工具 axios 二次封装  utils 去找 request.js

   baseURL 

   timeout 20000

6. 请求拦截器 Server.interceptors.request.use

   if store.getters.token

   config.hearders.token = store.getters.token

   响应拦截器 response

   const res = response.data

   if res.code !== 20000 && res.code !== 200

   Message({

   ​	message: res.message || 'Error',

   ​	type: 'error',

   ​	type: 'error',

   ​	duration: 5 * 1000

   })

   utils工具 一共修改三处地方 时间timeout token &&

   20000 是mock模拟数据 200是真实数据

   && 都满足 或 满足其一

   里面 if 可不写 后期不会碰到 500几

7. utils auth.js 存储介质 Cookies 现在都用LocalStorage

   Cookies 不安全 4kb限制 明文储存

8. api 接口请求函数 mock接口改为真实接口

   查看接口文档 02_Swagger接口列表

   接口文档我们用	第一个 最后一个权限

   权限地址 CV 地址栏

   后台API adminApi	前台API webApi

   index.controller: 后台登录与权限管理

   api user.js

9. 接下来看 是否用 vuex  用或不用 两套方案

   登录 vuex三连环

10. store modules user.js 合起来 少 getters 但有总getters

    在 index.js 配置 getters	相比以往vuex 小改变

11. vuex第六个概念 user.js export default namespaced: true 开启命名空间

12. A B两个空间 同时都有 getUser acions 会以为是同一个

    解决办法: namespaced:true 开启命名空间，开启之后 所有的getters mutations acions 在使用的时候，都要添加模块名 user/login

    优点：不同模块之间 可以重名合并时添加模块名标识

    这就是为什么 login 中 .dispatch user/login

    开启命名空间后，其实我们mapState和mapGetters就可以继续写数组了

    如果要写数组，第一名字要一致，第二需要添加两个参数

    

    没有开启命名空间但是使用了模块化写法：

    ...mapState({ token.state => state.user.token})

    使用了模块化也开启了命名空间：

    ...userState('user',['token']),

13. 登录 const state = {

    token: getToken(),

    name: '',

    avatar: ''

    }

    mutations RESET_STATE (state) Object.assign(state, getDefaultState(),{},{},{})

    assign 合并 后面所有合并至第一个

    注释掉 直接搞 state.token '' state.name '' state.avatar '' avatar 头像

    SET_TOKEN SET_NAME SET_AVATAR

    actions 注释修改为async

    async login({commit}, userInfo){

    ​	const {username, password} = userInfo

    ​	try const result = await login({username:username.trim(),password: password})

    ​	if(result.code \=== 20000 || result.code \=== 200)

    ​	const  {data} = result

    ​	commit('SET_TOKEN', data.token)

    ​	setToken(data.token) //模拟token 持久化存储

    ​	登录时候没有存储	自动登录才存储 现用cookie替代着

    ​	return 'ok'

    else return Promise.reject(new Error('登录失败'))

    catch Promise.reject(new Error('请求登录失败'))

    }

    自动登录 持久化存储 LocaolStorage

14. 修改 mutations SET_USERINFO(state,userInfo){

    state.name

    state.

    }

    actions getInfo 注释掉解构 

    commit SET_USERINFO, response.data

    resolve(data)

15. actions async getInfo({commit, state}) {

    try const result = await getInfo(state.token)

    if result.code 20000 || result.code 200

    commit SET_USERINFO result.data	return 'ok'

    else return Promise.reject(new Error('获取用户信息失败'))

    catch return Promise.reject(new Error('请求获取用户信息失败'))

    }

16. async logout({commit,state}){

    try const result = await logout(state.token)

    if result.code 20000 || result.code === 200

    removeToken()

    resetRouter()

    commit RESET_STATE

    return 'ok'

    }

17. async resetToken({ commit })

    return new Promise(resolve => {

    removeToken()

    commit('RESET_STATE')

    })

## 2. 点击登录请求返回404,配置代理解决登录404及修改下拉当中的首页和首页的中文显示

1. 浏览器 登录页面 登录请求失败 真实接口需要配置转发代理

2. vue.config.js  注释 before: require('./mock/mock/')

   代理 可以去 脚手架官网或者webpack查

   devServer .proxy	有些不全没有重写

   webpack 开发中 Serve(devServer) devserve-proxy

   proxy: {

   ​	'/dev/api': {

   ​	target: 'http://39.98.123.211',

   ​	pathRewrite:{	//写不写看 地址末尾有没有dev-api

   ​	'^/dev-api': ''

   }	

   }

   }

   重启服务器 修改配置文件必须重启

3. 测试：登录 登录进去代表配置成功 有1个报错 Dup

4. 把nav当中右侧下拉当中改为首页和退出登录

   layout index.vue vif device===;mobile

   sidebar	main-contain fixed-header

   fixedHeader 溯源 settings

   navbar	app-main

   侧边栏sidebar AppMain NavBar	去找NavBar

   NavBar 删除多余的 github ...

   2个 一级路由组件 登录和layout  app.vue 中切换

   app-main 二级中再切换

5. 至此 登录就完成了 熟悉潘家臣模板

## 3. 

1. 商品管理架子需要搭出来	测试管理新加权限

2. dashboard 二级路由 login 一级路由

3. 在views 新建 product 

   其下 再建 attr List.vue vue scss	sku spu trademark

   继续初始化 sku spu trademark 共 4 个路由组件

4. 配置相应的路由 router index.js 照猫画虎 配路由

   redirect 往二级路由	children

   在children 里面继续 { } 注释掉  redirect

   侧边栏根据你 配置的路由 遍历生成的

   这样去重定向，其实业务是可以实现的，但是侧边栏会多一行空白的

   因为潘佳辰是遍历路由动态生成的侧边栏，多一个路由，侧边栏就会多一个

5. 配置我们自己的路由

   {

   ​	path: '/product',	一级路径

   ​	component: Layout,

   ​	redirect: '/product/trademark/list',

   ​	children: [

   ​	{

   ​		path: 'trademark/list',

   ​		component:()=>import('@/views/product/trademark./List')

   ​	},

   {

   ​		path: 'attr/list',

   ​		component:()=>import('@/views/product/attr./List')

   ​	},

   ... 陆续配置相应的路由

   ]

   }

6.  16：22 name Dashboard 权限相关

7. 判断二级路由 大于 一个 一级路由才会显示出来

8. /product 添加 name 'Product' meta: { titel" '首页', icon:}

   children: [ ]

   名字一定要一样 name  名字一错 后期权限一定错

9. 商品管理 点击链接会禁掉 所以写不写 redirect无所谓

   点击不会影响路径的改变 点击二级才会地址栏改变

   商品管理 下面 4个 动态添加了router-link to

10. layout Sidebar Link.vue 动态路由

    SidebarItem.vue 组件自调用 递归调用

11. 面包屑 Dashboard 层层溯源 修改 title
12. 想不通的 干脆不想 重点放在 侧边栏和登录





