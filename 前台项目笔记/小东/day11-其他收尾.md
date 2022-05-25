## 1. 查看订单 完成个人中心静态及添加二级路由组件 选中路由链接高亮

1. 个人中心组件Center 捞 至 pages

2. router toure.js 配置Center路由  引入 path component 

   import Center from '@/pages/Center'

   ```js
     {
       path: '/center',
       component: Center,
     },
   ```

3. 订单中心 我的订单 团购订单

4. pages Center 新建文件夹 我的订单MyOrder 团购订单GroupOrder

5. 组件MyOrder GroupOrder 初始化

6. router routes.js  center路由 配置子路由 

   children:[ ]

   ```js
     {
       path: '/center',
       component: Center,
       children:[
         {
           path: 'myorder',
           component: MyOrder
         },
         {
           path: 'grouporder',
           component: GroupOrder
         }
       ]
     },
   ```

7. Center组件 左侧列表 右侧内容 猜你喜欢

   右侧内容分发 MyOrder 再捞样式 images图片捞MyOrder

8. Center组件 模板写 原模板为值 router-view

   我的订单 router-link to /center/myorder

   团购订单 router-link to /center/grouporder

   ```html
       <dt><i>·</i> 订单中心</dt>
       <dd>
         <router-link to="/center/myorder">我的订单</router-link>
       </dd>
       <dd>
         <router-link to="/center/grouporder">团购订单</router-link>
       </dd>
   ```

9. router route.js 添加重定向路由

   ```js
     {
       path: '/center',
       component: Center,
       children:[
         {
           path: 'myorder',
           component: MyOrder
         },
         {
           path: 'grouporder',
           component: GroupOrder
         },
         {
           path: '',
           redirect: 'myorder'
         }
       ]
     },
   ```

10. 我的订单 or 团购订单 当前高亮 右击 检查 router-link-active

    Center找样式 dd里面 .router-link-active { color:hotpink;}

    ```css
          dd {
            margin: 0 0 6px;
            border-bottom: 1px solid #ededed;
            text-align: center;
    
            .router-link-active{
              // color: hotpink;
              color: white;
              background: hotpink;
            }
          }
    ```

## 2. 动态展示订单列表及合并单元格 只出现一行 rowspan

1. 动态渲染 发请求 api index.js 请求获取订单列表

   export const reqOrder (page,limit)

   return request({

   url /order/auth/${page}/${limit} method get

   })

   ```js
   // 请求获取订单列表
   export const reqOrderInfo = {page,limit} => {
     return request({
       url: `/order/auth/${page}/${limit}`,
       method: 'get'
     })
   }
   ```

2. MyOrder组件 发请求 mounted this.getorderInfo() methods 定义该函数

   async getOrderInfo()

   自己构造参数 data return page:1 limit: 3

   try const result = await this.$API.reqOrderInfo(this.page,this.limit)

   if result.code 200 data 保存 //orderInfo: {} 

   直接保存数组orderList total:0

   this.orderList = result.data.records

   this.total = result.data.total

   else alert '获取订单信息失败'

   catch

   alert '请求获取订单信息失败'

   ```js
     mounted(){
       this.getOrderInfo();
     },
     methods: {
       async getOrderInfo(page=1){
         try {
           const result = await this.$API.reqOrderInfo(this.page, this.limit)
           if(result.code === 200){
             this.orderList = result.data.records;
             this.total = reuslt.data.total;
           }else{
             alert("获取订单信息失败")
           }
         } catch (error) {
           alert("请求获取订单信息失败")
         }
       }
     }
   ```

3.  瞅一眼 f12 vue工具  order orderList 查看数据

4. MyOrde 遍历我的订单 .orders 2个table 留1个

   .order-item vfor order orderList :key order.id

   thead 创建时间 订单编号 order.createTime order.outTradeNo

   图片 删除 死数据

   tbody 按行走看tr 一个商品是一行 遍历tr

   f12 看数据 orderList orderDetailList

   vfor item order.orderDetailList :key detail.id

   td  5个 对应一行 5个 rowspan 合并干掉

   1td 图片 :src detai.imgUrl style h100 w80 detail.skuName detail.skuNum

   2td 收货人 order.consignee 总金额 order.totalAmount 

   在线支付 order.paymentWay === 'ONLINE'?'在线支付':'货到付款'

   已完成 order.orderStatusName 刷新 看console错误

   合并单元格 tr第一个展示自己 后面4行一样不显示 第一行后几行合并

   1td 不关心 数据进来都会变化

   后面td 只有第一行展示 vif index==='0' :rowspan=order.orderDetailList.length

   vif 指令每个都要写 解析5次 效率不高 写div外层包裹 vif index === "0" 解析1次

   Header 我的订单 router-link to /center

   出现bug vif放div里面没效果 因为table里面不允许放div 解构改变

   解决办法 div 换成 template 

   template是h5新加html标签 不影响其他结构

   没有自己实际意义和结构 只是让包含里面成一个整体

   插槽 数据定义在父组件 从父组件传入子组件

## 3. 订单列表的分页器使用 合并请求函数和自定义事件的回调

1. 分页器 component Pagination

2. pages Myorder .choose-order <Pagination 

   事件 @changePageNo="changePageNo" 自定义事件$event 默参

   四个数据 背下来

   :currentPage=page,:total=total,:pageSize=limit,:continueNo=5

3. methods 定义 changePageNo(page) this.page=page this.getOrderInfo()

   省略changPage函数 this.page=page 写入getOrderInfo(page = 1 )

   第一次调出问题 没传参会出问题 形参需要有默认值 省写节省效率

4. 测试：分页器是否能用

5. 前台项目 杀青了

6. 图片懒加载 路由懒加载

## 4. 图片懒加载

> (1)    还没有加载得到目标图片时, 先显示loading图片
>
> (2)    在<img>进入可视范围才加载请求目标图片

1. 请求没回来 图片是白板 用户体验不好 滚动条没下来 图片不加载

   资料 前台课件 Vue电商项目 npm i vue-lazyload

2. 引入并配置loading图片  捞走放入 main

3. 自己找一张图片 gif 改名loading 扔至  assets imgages

4. 所有动态图片 :src 换成 v-lazy

5. Search组件 :src 换成 v-lazy

## 5. 如果没登录 访问用户相关页面将跳转登录页 并在登录后跳转之前想去而没到的页面 Vue Router

1. 全局守卫：如果是跳转多个页面都要进行同一检测,那么必然使用的是全局守卫(前置)

2. router routes.js 88H else 去掉next() if(to.path)

   如果没登录，访问（交易相关、支持相关、用户中心相关 ）跳转到登录页面

   登录后会自动跳转前面想去而没到的页面

   判断路径是不是以 /trade /pay /center 开头

   indexof

   startsWith

   let target = to.path

   if target.indexOf('./trade') === 0 || target.startsWith('/pay') || target.startsWith('./center')

   next('./login?redirect=' + target) 添加query参数 携带目的地

   else next()

3. Login组件 methods login alert "登录成功，准备自动跳转首页"后添加

   let redirect = this.$route.query.redirect || '/'

   this.$router.push(redirect)

4. 测试：点击我的订单 通过跳转登录页 登录后跳转到之前点击位置

## 6. 路由独享守卫和组件内守卫

1. 只有携带了skuNum和sessionStorage内部有skuInfo数据，才能看到添加购物车成功地界面

2. 路由独享守卫 beforeEnter:(to,from,next)

   保护 添加购物车成功地页面 

   router routes.js /addcartsuccess

   beforeEnter: (to, from, next) => {  from detail to

   let skuNum = to.query.skuNum

   let skuInfo = sessionStorage.getItem('skuInfo_key')

   if(skuInfo&&skuInfo) next()

   else next(false) 简单false 卡在原地 也能 '/'

   这能保证必须携带这参数才能跳转至addcartsuccess

3. 组件内守卫 组件内部去配置

   beforeRouteEnter(to, from, next)

4. addCartSuccess组件 配置组件内守卫，效果最终和路由独享守卫一样的

   只是配置时间先后不同

   beforeRouteEnte(to, from, next)

   在渲染该组件地对应路由被 confirm 前调用

   不！能！获取组件实例 'this'

   因为当守卫执行前，，组件实例还没被创建

5. next(vm => {

   通过 'vm' 访问组件实例

   console.log(vm.skuInfo)

   })

   F12 console 打印

## 7. 路由独享守卫实现特定页面的跳转

1. 只有从购物车界面才能跳转到交易界面（创建订单）

   router index.js /trade beforeEnter: (to, from, next) =>

   if from.path === '/shopcat' next()

   else next(false)

2. 只有从交易界面（创建订单）页面才能跳转到支付页面

   /pay

3. 只有从支付页面才能跳转到支付成功页面

   /paysuccess

## 8. 路由懒加载 *** routes.js 异步组件在路由组件的使用

> 路由懒加载特点
> 		每个路由组件打包会打包成一个单独的文件
> 		第一次访问哪一个路由组件，再去加载哪一个打包好的文件

> import()函数
>
>  import xxx from './xxx' 这样的引入方式，我们称作同步引入或者静态引入
>
> 1. 它不能动态引入(什么时候用什么时候引)
>
> function fn(){
>
> import xxx from './xxx'
>
> }
>
> fn() 这样的方式是错误的
>
> 2. 这样的引入方式，会让webpack一次性把引入的所有组件都打包到我们的主文件
>
> 浏览器在访问的时候，加载主文件，就已经把所有的组件加载了
>
> 由于上述两个特点的存在，这样的写法后期很少用，会造成浏览器加载页面效率变低
>
> Network JS app.js 代表自己写的代码  chunk-vendors.js 代表第三方的包
>
> 
>
> import() 这个东西我们称作动态引入或者异步引入，它返回的是promise
>
> 1、可以实现异步引入组件（异步组件），也叫动态引入组件
>
> 2、如果采用import函数去引入组件，
>
> ​	1> 所有采用import('./xxx')引入的组件都会单独的打包成一个小文件 不会打包进入主文件
>
> ​	2> 当我们第一次在浏览器访问到这个组件的时候，此时才会调用import函数，真正的去加载对应的小文件
>
> 由于它有上述2个特点，所以我们使用路由懒加载。
>
> function fn(){
>
> ​	import('./xxx').then(() =>{
>
> ​	module.a
>
> ​	module.b
>
> }).catch((error) => )
>
> ​	
>
> }
>
> fn()

1. router routes.js 注释 import Home from '@/pages/Home'

   const Home = () => import('@/pages/Home')

   注册组件的时候也可以是一个函数

   component: () => import('@/pages/Home')

   这个函数，第一次访问的时候才会调用，调用的时候才会真正的去加载

2. f12 Network 访问Home页面 会加载 app.js 1.9MB 0.js 541kb Home组件

   如果没有懒加载 全部加载 大蛋糕 app.js 2.4MB

   Search () => import('@/pages/Search') app.js 1.7MB 1.js 218kb

3. 代码分块点 Vue Router 路由懒加载

## 9. 表单验证的使用 vee-validate@2

1. 公司一般在src 新建plugins文件夹存放插件 validate.js lazyload.js也可以放入里面

2. 引入import

3. 基本使用

   单独验证

   

   整体验证

4. Register组件 手机号 input v-model phone name phone

   name必须要写，作为验证字段名 v-validate是验证器的意思

   后面对象写的 验证规则 :class invalid errors.has('phone') 错误对象

   invalid 非法的 主要看对象里面写啥 内置规则 required 必须 regix正则

   手机号验证规则 搜

   span 错误信息 class error-msg {{errors.first('phone')}}

   validate.js 记得引入 Vue

5. 注册新用户 测试 1 

6. 提示文本信息本地化 

   import zh_CN from 'vue-validate/dist/locale/zh_CN' 引入中文message

   ​					 验证器	本地化	国家语言标识

   VeeValidate.Validator.localize('zh_CN', {

   ​	messages: {

   ...zh_CN.message,

   is: (field) => `${field}必须与密码相同`  自己添加了一个内置规则的message

   },

   attributes: { // 给校验的field属性名映射中文名称

   phone: '手机号',

   code: '验证码'

   }

   })

7. 自己写的正则不合规 你认为的正则 三大运营商并不认可 一般搜

8. Register组件 修改验证码 

   v-model code name code 

9. 接着 登录密码 模板复制过来 btn不要 password regex /^w{6,20}$/

   找配置 password 映射成中文 密码

10. 确认密码 v-model password2 name password2 v-validate {is:password}

    配置 password2 确认密码

11. 同意协议 验证是否打钩 没有验证规则

12. 自定义规则 专门去验证 协议打钩与否

    extend 'agress' 验证方法 错误信息

    validate: value=>{return value},

    getMessage: field => field + '必须同意'

13. 验证模板 捞到 同意注册协议 v-validate {agree:true}

    isChecked 映射中文 协议

14. 手机号 验证码 点击注册不了 干掉简单的 注册if验证

15. 每个单个验证做完后 完成注册需要添加 整体验证

16. Register const success = await this.$validator.validateAll()

    if(success) let { phone, password, code } = this

    try await 

17. 整个前台项目 杀青了 路由懒加载 图片懒加载

## 10. 打包dist 购买服务器及配置服务器

1. 打包dist文件	npm run build

2. 服务器 一般ECS共享型 云服务器

   服务器的相关配置 

   阿里云 最新活动 新人特惠专享  热卖产品新人特价

   运营 宣传的 运维 维护的 超过20% 按分钟算 20个点用完扣钱

   不要搞 ESC突发性能型t6 很坑 最基本low 1核2G

   

   地域 华中 华北 华南

   可用 不用选

   操作系统选择 Linux  linux是内核 CentOS 7.8 64 7.无所谓

   主要选择系统 

   

   腾讯云 最新活动 2核2G 50 2核4G 120

   买好去 控制台 云服务器 最近访问地区要选对

   相当于给你分配了 一台虚拟机 

   刚买服务器 第一次进入一定要重置密码 更多里面

   安全组 主要打开服务器端口 修改规则

   服务器 80端口必须打开 80端口 处理http请求

   一键放通：常用端口一次放通  

   ssh 对应 22端口 

   ICMP 邮件端口

   3306 mysql端口 

   27017 MongoDB端口

   编辑 里面 可以追加 协议端口安全组配置好了 点击保存

   至此 服务器买好配置好 配置 主要重置密码和安全组

   ## 11. Xshell连接云服务器 练习linux命令

   1. Xshell 新建 名称 tengxunyun 协议SSH 主机 公网IP

      端口号 22 弹框输入登录的用户名 打钩 输入密码

      root@VM-0-2-centos

      linux 分文件夹  windows分盘

      / 根目录 文件夹开枝散叶 最终倒立的树模型

   2. cd / 根目录 cd .. 上一层 ls 查看文件夹 关注 root

   3. ~ 就是 /root 家目录 干活在这儿干 / 相当于 windows 系统C盘

   4. ls ly cd ly/ cd gulishop-client/

   5. rm -rf ly 全部干掉了 ls 刷新还有代表本地还有缓存 ctrl+F5强刷

      500代表 服务器有问题 nginx

   6. mkdir ly

   7. cd ly/
   
   8. mkdir gulishop-client
   
   9. pwd 查看当前绝对路径
   
   10. 如何上传dist 至 云服务器

## 11. 通过Xftp上传dist到云服务器(不能访问,不知道返回谁)

1. Xftp 7 上传文件用的 绿色免安装 和Xshell 兄弟俩 出自同公司
2. 点击文件 点击新建 名称aaa 主机IP 协议 SFTP 端口22 连接
3. 弹框 输入 用户名 root 接着 输入密码
4. 左边桌面本地  右边 aaa云 打开 gulishop-client dist拖入
5. 功成身退 关闭  Xshell  ls查看 cdly/
6. 目前只是完成第一步 仍然访问不了 不应该出来
7. 服务器知道 访问的是80端口 服务器不知道返回什么
8. 配置服务器 返回 dist/index.html
9. yum remove nginx 删除云服务器的代理
10. nginx 代理 Web服务器
11. 生产环境 没有webpack 就没有webpack DevServer 需要靠 nginx
12. 讲至 nginx简介