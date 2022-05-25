## 1. 结算 - 完成交易页面

1. 交易页面静态 Trade pay paySuccess 至 pages

2. 配置3个静态路由 router routes.js 引入 import 并配置 /trade /pay / paysuccess

   ```js
     {
       path:'/trade',
       component:Trade
     },
     {
       path:'/pay',
       component:Pay
     },
     {
       path:'/paysuccess',
       component:PaySuccess
     },
   ```

3. ShopCart  结算 注释 a标签  route-link to /trade

   ```html
       <div class="sumbtn">
         <!-- <a class="sum-btn" href="###" target="_blank">结算</a> -->
         <router-link class="sum-btn" to="/trade">结算</router-link>
       </div>
   ```

4. 获取订单交易信息 获取用户地址信息 发2个请求

   接口请求函数 api index.js 获取收货地址 reqGetUserAddress url /user/userAddree/auth/findUserAddressList method get

   获取交易信息 reqGetTradeInfo url /order/auth/trade method get

   ```js
       // 请求获取收货地址
       export const reqGetUserAddress = () => {
         return request({
           url: 'user/userAddress/auth/findUserAddressList',
           method: 'get'
         })
       }
   
       // 请求获取交易信息
       export const reqGetTradeInfo = () => {
         return request({
           url: 'order/auth/trade',
           method: 'get'
         })
       }
   ```

5. vuex三连环 store modules user.js

   state addressList: []

   mutation RECEIVE_ADDRESSLIST(state,addressList) state.adressList = addressList

   actions 获取收货地址 async getUserAddressList 

   try const result await reqGetUserAddress result.code 200 commit RECEIVE_ADDRESSLIST result.data

   else 获取用户收货地址失败 catch 请求获取用户收货地址失败

   ```js
     addressList: []
   
     RECEIVE_ADDRESSLIST(state,addressList){
       state.addressList = addressList
     }
   
   
     // 获取收货地址
     async getUserAddressList({commit}){
       try {
         const result = await reqGetUserAddress()
         if(result.code === 200){
           commit('RECEIVE_ADDRESSLIST',result.data)
         }else{
           alert('获取用户收货地址失败')
         }
       } catch (error) {
         alert('请求获取用户收货地址失败')
       }
     }
   ```

6. store modules 新建小store trade.js 初始化   

    **默认暴露别忘了** export default{state,mutations,actions,getters}

   大store index.js 中import 引入并合并入modules

   state tradeInfo: {} 

   mutation RECEIVE_TADEINFO(state,tradeInfo) state.tradeInfo = tradeInfo

   actions async getTradeInfo({commit})

   try const result = await reqGetTradeInfo()

   if result.code 200 commit RECEIVE_TRADEINFO result.code

   else alert 获取交易信息失败 catch alert 请求获取交易信息失败

   ```js
   const { reqGetTradeInfo } = require("@/api")
   
   const state = {
     tradeInfo: {}
   }
   
   const mutations = {
     RECEIVE_TRADEINFO(state,tradeInfo){
       state.tradeInfo = tradeInfo
     }
   }
   
   const actions = {
     async getTradeInfo({commit}){
       try {
         const result = await reqGetTradeInfo()
         if(result.code === 200){
           commit('RECEIVE_TRADEINFO',result.data)
         }else{
           alert('获取交易信息失败')
         }
       } catch (error) {
         alert('请求获取交易信息失败')
       }
     }
   }
   
   const getters = {}
   
   export default {
     state,
     mutations,
     actions,
     getters
   }
   ```

7. trade组件 发请求获取交易信息 mounted this.getUserAddressList() this.getTradeInfo()

8. methods 定义函数 getUserAddressList  this.$store.dispatch('getUserAddressList')

   getTradeInfo this.$store.dispatch('getTradeInfo')

   ```js
       mounted() {
         this.getUserAddressList();
         this.getTradeInfo();
       },
       methods: {
         getUserAddressList() {
           this.$store.dispatch("getUserAddressList")
         },
         getTradeInfo() {
           this.$store.dispatch("getTradeInfo")
         }
       }
   ```

## 2. 获取vuex当中的用户收货地址和交易信息 初始化展示交互界面

1. store modules Trade.js Getters detaiArrayList(state) return state.tradeInfo.detailArrayList || []

   ```js
       const getters = {
         detailArrayList(state){
           return state.tradeInfo.detailArrayList || []
         }
   ```

2. Trade组件 computed ...mapState addressList: state=>state.user.addressList,

   tradeInfo:state=>state.

   ...mapGetters(['detaiAddressList'])

   ```js
     computed: {
       ...mapState({
         addressList: state => state.user.addressList,
         tradeInfo: state => state.trade.tradeInfo
       }),
       ...mapGetters(["detailArrayList"])
     }
   };
   ```

3. trade组件 修改模板 H6 3.address clearFix 留1  vfor address addressList address.id

   收货人姓名 address.consignee  地址 adress.Address 电话号码address.phoneNumber

   ```html
     <div class="address clearFix" v-for="(address, index) in addressList" :key="address.id">
       <span class="username selected">{{ address.consignee }}</span>
       <p>
         <span class="s1">{{ address.userAddress }}</span>
         <span class="s2">{{ address.phoneNumber }}</span>
         <span class="s3">默认地址</span>
       </p>
     </div>
   ```

4. 支付方式 是假的 只有在线支付 配送方式是假的只有天天快递

5. 商品清单 2个ul.list chearFix 干掉 留一个 vfor detail detailArrayList detail.skuId

   图片 :src detail.imgUrl  style width 100px height 80px

   名字 detail.skuName 单价 detail.orderPrice 数量 detail.skuNum

   ```html
       <ul class="list clearFix" v-for="(detail, index) in detailArrayList" :key="detail.skuId">
         <li>
           <img :src="detail.imgUrl" alt="" style="width:100px;height:80px" />
         </li>
         <li>
           <p>{{ detail.skuName }}</p>
           <h4>7天无理由退货</h4>
         </li>
         <li>
           <h3>￥{{detail.orderPrice}}</h3>
         </li>
         <li>X{{ detail.skuNum }}</li>
         <li>有货</li>
       </ul>
   ```

6. Trade 买家留言 用户交互 textarea文本域 v-moel收集数据  message

   data return message 

   ```html
     <div class="bbs">
       <h5>买家留言：</h5>
       <textarea placeholder="建议留言前先与商家沟通确认" class="remarks-cont" v-model="message"></textarea>
     </div>
   ```

   ```js
     data(){
       return {
         message: "",
       }
     },
   ```

7. 发票信息 假的 几件商品 1 是动态数据 trade tradeInfo totalNum 

   总的数量tradeInfo.totalNum 总价 totalAmount 返现 运费 0

   ```html
       <div class="money clearFix">
         <ul>
           <li>
             <b>
               <i>{{ tradeInfo.totalNum}}</i>件商品，总商品金额
             </b>
             <span>¥{{ tradeInfo.totalAmount }}</span>
           </li>
           <li>
             <b>返现：</b>
             <span>0.00</span>
           </li>
           <li>
             <b>运费：</b>
             <span>0.00</span>
           </li>
         </ul>
       </div>
   ```

   应付金额 

   ```html
     <div class="price">
       应付金额:
       <span>¥{{ tradeInfo.totalAmount }}</span>
     </div>
   ```

8. 麻烦在于 收货人信息 商品列表

## 3. 点击收获地址切换默认地址

1. 收货人信息与收货人地址

2. Trade组件 username :class{selected:adress.isDefault === '1'}

   ```html
   <span class="username" :class="{ selected: address.isDefault === '1' }">{{ address.consignee }}</span>
   ```

   默认地址 vif address.isDefault === 1

   ```html
   <span class="s3" v-if="address.isDefault === '1'">默认地址</span>
   ```

   收件人和收件地址都能进行切换加给他爹 @changeIsDefault(addressList,address)

   ```html
     <div class="address clearFix" v-for="(address, index) in addressList" :key="address.id" @click="changeIsDefault(addressList, address)">
   ```

3. methods定义函数 点击切换默认地址 changeIsDefault(addressList,address)

   ```js
       // 点击切换默认地址 排他
       changeIsDefault(addressList, address){
         addressList.forEach((item) => (item.isDefault = "0"))
         address.isDefault = "1"
       },
   ```

4. 提交订单 上面 寄送地址 动态变化 计算属性 监听整个数组的变化

   什么时候用计算属性 数据要用但是不存在需要计算出来的

   什么时候用监听watch 一个数据引起另一个数据改变 这个数据必须存在

5. Trade组件计算默认地址 computed defaultAddress () 找不到 undefind就会假报错 So || {}

   computed defaultAddress () 

   return this.addressList.find(item=>item.isDefault==='1') || {}

   ```js
       // 计算下面的默认地址
       defaultAddress() {
         return this.addressList.find((item) => item.isDefault === "1") || {}
       },
   ```

   模板展示  寄送至 defaultAddress.userAddress .consignee .phoneNum

   .undefind 必然假报错

   ```html
     <div class="receiveInfo">
       寄送至:
       <span>{{ defaultAddress.userAddress }}</span>
       收货人：
       <span>{{ defaultAddress.consignee }}</span>
       <span>{{ defaultAddress.phoneNum }}</span>
     </div>
   ```

6. 寄送至 地址不存在 需要通过计算出来 computed 从数组中计算出来 

   计算属性 它永远是响应式的 至此 收件人信息 交互完成

## 4. 点击提交订单 提交订单跳转支付页面

1. 提交订单 发送请求 创建订单 后台返回订单编号 携带返回信息 跳到 支付页面

2. Trade组件 模板 注释 提交订单router-link to 写btn 

   btn class subBtn @click submitOrder

   ```html
       <div class="sub clearFix">
         <!-- <router-link class="subBtn" to="/pay">提交订单</router-link> -->
         <button class="subBtn" @click="submitOrder">提交订单</button>
       </div>
   ```

3. methods 定义函数 点击提交订单 submitOrder 发请求

4. api接口请求函数 请求提交订单 如果后台没接口 mock模拟

   reqSubmitOrder  tradeNo tradeInfo query参数

   url /order/auth/submitOrder?tradeNo=${tadeNo} 

   method post data tradeInfo

   ```js
   // 请求提交订单
   export const reqSubmitOrder = (tradeNo,tradeInfo) => {
     return request({
       url: `/order/auth/submitOrder?tradeNo=${tradeNo}`,
       method: 'post',
       data: tradeInfo
     })
   }
   ```

5. 不写vuex三连环 api引入 调用组件 这样不好 每个组件都需自己引

   不用vuex 所有api统一挂Vue原型

   > 默认暴露和引入
   >
   > ​	无论什么暴露方式，暴露出去的都是对象。只是形成的对象的方式不一样
   >
   > 默认暴露 出文件时候是一个对象，对象里面是以dafault为属性，以default后面的值为值的一个对象
   >
   > const a = 100
   >
   > export default a
   >
   > {
   >
   > ​	default: a
   >
   > }
   >
   > 默认暴露的引入方式 
   >
   > import { default as a } from './xxx' //默认暴露的真实完整引入写法
   >
   > import a from './xxx' //这行是上面的简写
   >
   >  
   >
   > 分别暴露
   >
   > 分别暴露形成的对象，是系统给你添加的花括号
   >
   > export const a = 100
   >
   > export cosnt b = {c:200}
   >
   > {
   >
   > a,
   >
   > b
   >
   > }
   >
   > 分别暴露和统一暴露的引入方式 本质是解构 从对象中拿东西
   >
   > import { a,b } from './xxx'
   >
   >  
   >
   > xxx最终拿到就是暴露出来的对象
   >
   > import * as xxx from './xxx'
   >
   > 
   >
   > 统一暴露
   >
   > 统一暴露形成的对象，是自己添加的花括号，可以认为是分别暴露的简写
   >
   > const a = 100
   >
   > const b = [1,2,3]
   >
   > export {
   >
   > ​	a,
   >
   > ​	b	
   >
   > }
   >
   > 暴露出去的就是export后面的对象

   main.js import * as API from '@/api'

   ```js
   import * as API from '@/api'
   ```

   beforeCreate Vue.prototype.$API =API 这下想在哪发请求就直接发请求

   ```js
   new Vue({
     beforeCreate(){
       Vue.prototype.$bus = this
       Vue.prototype.$API = API
     },
     router,
     store,
     render: h => h(App),
   }).$mount('#app')
   ```

6. Trade组件 methods  async submitOrder await this.$API.reqSubmitOrder

   准备参数 tradeNo 交易编号 tradeInfo 交易信息

   let tradeNo=this.tradeInfo.tradeNo

   let tradeInfo={} api文档示例直接粘贴 修改为 动态数据 最终用户确定好的交易信息

   this.defaultAddress.consignee this.defaultAddress.phoneNum this.defaultAddress.userAddress

   thi this.defaultAddress.message  this.detailArrayList

   try const result = await this.$API.reqSubmitOrder(tradeNo,tradeInfo)

   if result.code 200 要存一下数据的话 data return orderNo

   ```js
     data() {
       return {
         message: "",
         orderNo: '',
       };
     },
   ```

   this.orederNo = result.data

   alert 创建订单成功，准备跳转到支付页面 + result.data

   this.$router.push('/pay'>orderNo=' + result.data)

   else alert 创建订单失败

   catch 请求创建订单失败

   ```js
       // 点击提交订单
       async submitOrder() {
         // 准备参数
         let tradeNo = this.tradeInfo.tradeNo; //交易页面获取的tradeInfo本身就有
         // 最终用户确定好的交易信息
         let tradeInfo = {
           consignee: this.defaultAddress.consignee,
           consigneeTel: this.defaultAddress.phoneNum,
           deliveryAddress: this.defaultAddress.userAddress,
           paymentWay: "ONLINE",
           orderComment: this.message,
           orderDetailList: this.detailArrayList
         };
         try {
           const result = await this.$API.reqSubmitOrder(trade, tradeInfo);
           if (result.code === 200) {
             this.orderNo = result.data;
             alert("创建订单成功，准备跳转到支付页面" + result.data);
             this.$router.push("/pay?orderNo=" + result.data);
           } else {
             alert("创建订单失败");
           }
         } catch (error) {
           alert("请求创建订单失败");
         }
       }
   ```

   **报错201 前端请求参数有问题**

7. 测试：提交订单 跳转支付界面 订单号和应付金额是动态的 其他是假的

8. 支付之前需要再次发送请求 查询支付信息 支付信息不光有应付金额 还有动态数据 二维码

   > 总结：
   >
   > ​	使用vuex i.api ii.3连环 iii.dispatch iv. 数据存vuex
   >
   > ​	不使用vuex i.api ii.挂api iii. 组件当中直接$API调用接口请求函数 iv.数据存data

## 5. 支付页面

1. 传来的订单编号 拿下来 发送请求获取应付金额

2. Pay组件 显示订单编号 data return orderNo: '' 

   beforeMount(){ this.orderNo = this.$route.query.order }

   ```js
     export default {
       name: 'Pay',
       data(){
         return {
           orderNo: '',
   
         }
       },
       beforeMount(){
         this.orderNo = this.$route.query.orderNo
       }
     }
   ```

   订单号改为动态数据 orderNo

   ```html
       <div class="paymark">
         <span class="fl">请您在提交订单<em class="orange time">4小时</em>之内完成支付，超时订单会自动取消。订单号：<em>{{orderNo}}</em></span>
         <span class="fr"><em class="lead">应付金额：</em><em class="orange money">￥17,654</em></span>
       </div>
   ```

3. 应付金额 发送请求 api 请求获取支付信息 reqGetPayInfo 查看api文档 13 获取订单支付信息

   reqGetPayInfo (orderId)

   return request url /payment/weixin/createNative/${orderNo} method get 复制待用

   ```js
   // 请求获取支付信息
   export const reqGetPayInfo = (orderId) => {
     return request({
       url: `/payment/weixin/createNative/${orderId}`,
       method: 'get'
     })
   }
   ```

4. pay组件 mounted this.getPayInfo() methods定义函数 

   async getPayInfo

   try const result await this.$API.reqGetPayInfo(this.orderNo)

   data return payInfo: {}

   if result.code 200 this.payInfo = result.data

   else alert 获取订单信息失败 catch 请求获取订单信息失败 Vue查看 pay组件data数据

   ```js
       mounted(){
         this.getPayInFO()
       },
       methods: {
         async getPayInfo() {
           try {
             const result = await this.$API.reqGetPayInfo(this.orderNo)
             if(result.code === 200){
               this.payInfo = result.data
             }else{
               alert('获取支付信息失败')
             }
           } catch (error) {
             alert('请求获取支付信息失败')
           }
         }
       },
   ```

5. 模板动态数据展示 应付金额 payInfo.totalFree

   ```html
       <div class="paymark">
         <span class="fl">
           请您在提交订单
           <em class="orange time">4小时</em>之内完成支付，超时订单会自动取消。订单号：
           <em>{{orderNo}}</em>
         </span>
         <span class="fr">
           <em class="lead">应付金额：</em>
           <em class="orange money">￥{{payInfo.totalFee}}</em>
         </span>
       </div>
   ```

## 6. Element 简单的完整引入和按需引入方式

1. 弹框扫码支付 用组件库Element UI

2. 组件库 Element UI Vue PC端 移动端用不了

   安装 npm i element-ui -S	完整引入 按需引入 lodash 

   完整引入ElementUI 主要两行 引入main.js 声明使用插件 Vue.use(ElementUI);

   ```js
   // 完整引入element-ui
   import ElementUI from 'element-ui';
   import 'element-ui/lib/theme-chalk/index.css';
   Vue.use(ElementUI)
   ```

    Button 按钮 捞走 默认按钮 - 危险按钮 至 Pay组件 .pay-main 下面

   ```html
   <template>
     <div class="pay-main">
       <el-button>默认按钮</el-button>
       <el-button type="primary">主要按钮</el-button>
       <el-button type="success">成功按钮</el-button>
       <el-button type="info">信息按钮</el-button>
       <el-button type="warning">警告按钮</el-button>
       <el-button type="danger">危险按钮</el-button>
   ```

   测试：ElementUI能不能用 提交订单后 第一行 颜色按钮出现就引入成功

   type 封装了颜色

    蓝- 主要按钮  绿 - 成功按钮 灰 - 信息按钮 橙 - 警告按钮 红 - 危险按钮

   size 按钮大小 mini 小

   icon el-con-delete 删除  icon el--con-edit 修改 el-icon-plus 添加 

    icon图标 直接通过设置类名为 `el-icon-iconName` 来使用即可

3. Vant UI 是做移动端Vue组件库

4. 开发 完整引入 生产 按需引入 因为按需引入可以减少打包得体积，效率比较高

   生产时候 即使 100个组件 你用了80个都得按需引入，节流优化 保证效率

   Element UI babel是用来编译 将 ES6 转为 ES5语法

    借助babel-plugin-component 引入需要的组件，以达到减少项目体积的目的

5. 按需引入 安装 npm install babel-plugin-component -D

    .babelrc 在脚手架5中叫 babel.config.js 引入.babelrc的"plugins"

   ```js
   module.exports = {
     presets: [
       '@vue/cli-plugin-babel/preset'
     ],
     "plugins": [
       [
         "component",
         {
           "libraryName": "element-ui",
           "styleLibraryName": "theme-chalk"
         }
       ]
     ]
   }
   
   ```

6. 按需引入 引入部分组件，需要在 main.js  14:15

   import {Button,MessageBox,Message} from 'element-ui'

   Vue.use(Button)

   Vue.component(Button.name,Button)

   ```js
   // 按需引入element-ui
   // 以后开发的时候我们可能用完整引入，但是在生产上线的时候都要换为按需引入
   // 因为按需引入可以减少打包的体积，效率比较高
   import { Button,MessageBox,Message } from 'element-ui'
   // Vue.use(Button)
   Vue.component(Button.name,Button)
   ```

   修改外部的配置文件就得重新启动服务器

5. 完整组件列表和引入方式 Element里面组件分为两种组件 MessageBox Vue.use(）找不到MessageBox

   而是 Vue.prototype.$

   > elment-ui当中其实包含了两大类组件：
   >
   > ​	1、可以直接写标签使用的组件，比如Button，这样的组件引入之后
   >
   > ​	通过Vue.component或者Vue.use去注册即可写标签使用
   >
   > 
   >
   > ​	2、不能直接写标签，它本质上并不是一个组件，而是一个对象或者函数
   >
   > ​	比如MessageBox 它不能使用Vue.component或者Vue.use去注册
   >
   > ​	它想要去使用，必须挂在Vue的原型身上，以后在每个组件当中都可以调用这个函数或者
   >
   > ​	拿到这个对象

8. MessageBox 并不是一个组件 字面意思是 消息盒子 但是是弹框 

   暴露出来并不是组件而是一个对象 函数

   怎么使用呢？

   ~~Vue.prototype.\$loading = Loading.service; 干掉 用不上~~

   Vue.prototype.\$msgbox = MessageBox; 

   Vue.prototype.​\$alert = MessageBox.alert; 

   Vue.prototype.​\$confirm = MessageBox.confirm; 

   Vue.prototype.​\$prompt = MessageBox.prompt; 

   ~~Vue.prototype.​\$notify = Notification; 干掉 用不上~~

   Vue.prototype.$message = Message; 消息相当于alert 比alert更好看

   挂在Vue原型上面 相当于 所有组件都能调用

   ```js
   Vue.prototype.$msgbox = MessageBox;
   Vue.prototype.$alert = MessageBox.alert;
   Vue.prototype.$confirm = MessageBox.confirm;
   Vue.prototype.$prompt = MessageBox.prompt;
   Vue.prototype.$message = Message;
   
   ```

7. MessageBox 例子自定义 一般情况用 alert 或者 confirm 去做自定义

8. Options 配置项 可以自己配置

   传入了3个参数，第3个参数配置	不用背，写到哪 直接查

11. 扫码支付弹框 是图片 嵌入img图片  捞走 使用HTML片段

12. Pay组件 立即支付 注释 Router-link to 写btn按钮 绑定@click pay

     ```html
         <div class="submit">
           <!-- <router-link class="btn" to="/paysuccess">立即支付</router-link> -->
           <button class="btn" @click="pay">立即支付</button>
         </div>
     ```

10. methods 定义函数 pay()

    ```js
        // 点击立即支付
        pay() {
          this.$alert('<strong>这是 <i>HTML</i> 片段</strong>', 'HTML 片段', {
              dangerouslyUseHTMLString: true
            });
        }
    ```

11. 报错需要 在import {引入Message}

12. 测试：添加商品 结算 支付 看有没有弹框

13. 第二个参数是标题 HTML片段是标题 修改

    右上角 × 去掉 得去自定义 Options showClose 第三个配置对象改为 false

    两个按钮 showCanceButton ShowConfirmButton

    文本	cancelButtonText	confirmButtonText

    居中	center 默认false 改为 true

    图片	等会再弄，没辣么简单

## 8. 二维码

1. codeUrl 后台给的微信二维码

2. Pay组件 methods 点击立即支付 pay()

   > 支付流程：
   >
   > ​	1、生成二维码图片
   >
   > ​	2、使用消息盒子弹出二维码 采用第三方库 qrcode
   >
   > ​	npm i  --save qrcode 但是github去搜索 node-qrcode 用于把返回的codeUrl生成图片	二维	码
   >
   > ​	3、轮询支付状态

3. npm 搜索 qrcode 安装 npm i --save qrcode

   Usage 用例 canvas画布 画笔 橡皮擦 笔纸橡皮差 一堆API

   echarts 底层用的canvas 进行的二次封装

   原生JS死于兼用 各大浏览器 jQuery 解决了95%的兼容问题

   Angular react Vue出来了数据驱动页面 操作虚拟页面 diffing算法

   jQuery死翘翘   js操作真实DOM 效率不高

4. With async/await 

   ```js
   const generateQR = async text => {
     try {
       console.log(await QRCode.toDataURL(text))
     } catch (err) {
       console.error(err)
     }
   }
   ```

5. Pay组件 methods  async pay()

   try cosnt imgUrl = await QRcode.toDateURL(this.payInfo.codeUrl)

   引入 Import QRCode from "qrcode"

   ```js
   import QRCode from 'qrcode'
   ```

   this.$alert(

   ​	`<imgsrc="${imgUrl}"`

   )

   ```js
       // 点击立即支付
       async pay() {
         // 1、生成二维码图片
         const imgUrl = await QRCode.toDataURL(this.payInfo.codeUrl);
         // 2、使用消息盒子弹出二维码
         this.$alert(
           `<img src="${imgUrl}">`, 
           "请使用微信扫码支付", 
         {
           dangerouslyUseHTMLString: true,
           showClose: false,
           showCancelButton: true,
           cancelButtonText: "支付遇到问题",
           confirmButtonText: "我已成功支付",
           center: true
         });
       }
   ```

## 9. 轮询用户支付状态 循环定时器 2s发一次请求

1. 生成二维码 MessageBox 二维码展示

2. 如何判断用户 是否扫了 是否支付 二维码 什么时候支付 

3. 引入 轮询概念 给后台发请求 返回支付状态码

4. 设置定时器 setInterVal  每一个订单只能开启一个定时器 防止叠加异常

5. 判断 !this.timer 保证定时器只有一个 

   if(!this.timer) this.timer = setInterval (async ()=>{)

   请求获取支付状态 发请求 要写api

   try const result = await this.$API.reqPayStatus(this.orderNo)

   if result.code 200 //支付成功  i.跳转之前清除定时器 ii.关闭messagebox iii.跳转成功页面

   i. clearInterval(this.timer) 干掉任务，但还没清完 变量值还在 this.timer = null 才算清完

   定时器的任务是 回调函数→管理模块管理  清理定时器一定要清全清完整

   清除计时器用 clearTimeout 本质一样 但一般配对来写 最终效率一样，一般不这么写

   ii. 打印messagebox发现其有close方法 this.$msgbox.close()

   iii. this.$router.push('paysuccess') 跳转到支付成功界面

   二维码不是200 什么都不敢 不然难不成一直交钱一直交钱

   catch this.$message.error '请求获取支付状态失败'

   ,2000

   ```js
       // 点击立即支付
       async pay() {
         try {
           // 1、生成二维码图片
           // 1、生成二维码图片
           const imgUrl = await QRCode.toDataURL(this.payInfo.codeUrl);
           // 2、使用消息盒子弹出二维码
           this.$alert(`<img src="${imgUrl}">`, "请使用微信扫码支付", {
             dangerouslyUseHTMLString: true,
             showClose: false,
             showCancelButton: true,
             cancelButtonText: "支付遇到问题",
             confirmButtonText: "我已成功支付",
             center: true
           });
   
           // 第三步：轮询支付状态
           // 这样可以保证定时器只有一个
           if(!this.timer){
             this.timer = setInterval(async() => {
               try {
                 const result = await this.$API.reqPayStatus(this.orderNo)
                 if(result.code === 200){
                   // 保存支付状态，为了后期用户点击我已成功支付按钮的时候有判断条件
                   this.payStatus = 200
                   // 支付成功
                   // 清除定时器
                   clearInterval(this.timer)
                   this.timer = null
                   // 关闭messagebox
                   this.$msgbox.close()
                   // 跳转到支付成功界面
                   this.$router.push('/paysuccess')
                 }
               } catch (error) {
                 this.$message.error("请求获取支付状态失败")
               }
           }, 2000)
           }
         } catch (error) {
           alert("生成二维码失败")
         }
       }
     }
   ```

   data return 保存支付状态 payStatus 200下面 this.payStatus=200 为后期判断条件

   ```js
     data() {
       return {
         orderNo: "",
         payInfo: {},
         payStatus: 0
       };
     },
   ```

6. 前置5 api index.js 请求获取支付状态 reqPayStatus 看文档 14 查询订单 状态

   return request url /payment/weixin/queryPayStatus/${orderId} method get

   ```js
   // 请求获取支付状态
   export const reqPayStatus = (orderId) => {
     return request({
       url: `/payment/weixin/queryPayStatus/${orderId}`,
       method: 'get'
     })
   }
   ```

7. 提交支付 弹框 f12 查看 Network 2s 一个请求 205 支付成功 跳转

## 10. 两个按钮点击逻辑

1. 重新点击发起订单 添加购物车 去购物车结算 提交订单 立即支付 开始轮询

2. 清除计时器的话 进入不了if

3. 点击 支付遇到问题 会一直发送请求

4. 问题 无论点击 支付遇到问题 or 我已成功支付 都会关闭msgbox弹框

5. 效果: 点击 我已成功支付 弹框还在 弹出消息 支付成功界面只能掏钱过去

6. 点击 不关闭 提示消息 

7. Pay组件 methods Pay this.$alert().then 对应的是点击确定按钮的逻辑

   console.log(111)

   .catch对应的是点击取消按钮的逻辑

   console.log(222)

   ```js
     /* 
       - .then对应的是点击确定按钮的逻辑，也就是说点了确定代表成功
       - .catch对应的是点击取消按钮的逻辑，也就是说点了取消代表失败
       - 如果我们不添加下面的.then .catch，相当于点击取消的时候没有处理失败的情况，就会报错
       - 我们添加.then和.catch目的只是为了让点击取消的时候，不会报错，仅此而已
       - .then .catch这两个回调，按道理讲，我们可以去写我们的逻辑，但是这两个回调并不能阻止消息盒关闭。
       - 因此，我们不会把逻辑写在这两个回调当中
   
     */
   }).then(() => {console.log(111)}).catch(() => {console.log(222)})
   ```

   不写后续 .then .catch  成功没问题 点取消(支付遇到问题) 会报错 没有处理失败的情况

   .then .catch 目的只是为了点击取消的时候，不会报错 仅此而已

   只加一个 .catch也行 写全就全加上 .then .catch

   .then .catch 这两个回调，按道理讲，我们可以去写我们的逻辑，但是这两个回调并不能阻止消息盒子关闭，所以我们不会把逻辑写在这两个回调当中

8. 取消关闭 逻辑写在 beforeClose msgbox关闭前的回调

   配置项里面 配 beforeClose 代表消息盒子关闭之前，会阻止盒子的关系，后续手动关闭

9. 新注册账号 没有地址 提交支付 但是没有收货人地址 查看 vue组件 pay ==16:32==

10. 点击成功支付 提示弹框 支付遇到问题 提示弹框

11. beforeclose:(cation, instance, done) => 不能用funcetion  this拿不到组件对象

    想要 this是组件对象就得写 箭头函数

    if(cation === 'confirm') 点击确定的逻辑

    if !this.payStatus 代表没支付 this.$message.warning '赶紧付钱!!!'

    不许要写else 成功的话 下面就跳过去了

    先.then .catch 再进入 beforeclose 阻止盒子的关闭

    else if action===cancel  取消的逻辑

    this.$message.warning '请联系尚硅谷前台'

    关闭消息盒子msgbox 清除定时器 可能这个人不买

    done()  clearInterval(this.timer) this.timer=null

    ```js
      // 代表消息盒子关闭之前,会阻止盒子的关闭,后续我们可以手动关闭
      beforeClose:(action, instance, done) => {
        if(action === 'confirm'){
          // 点击确定的逻辑
          if(!this.payStatus){
            // 代表没支付
            this.$message.warning('赶紧付钱!!!')
            this.$message({
              type: 'warning',
              message: '赶紧付钱!!!',
              duration: 500,
            })
          }
        }else if(action === 'cancel'){
              // 点击取消的逻辑
              // this.$message.success('请联系尚硅谷前台小姐姐')，
              this.$message({
                type: 'error',
                message: '请联系尚硅谷前台',
                duration: 3000,
              })
              // 清除定时器
              clearInterval(this.timer)
              this.timer = null
              // 关闭消息盒子
              done()
            }
      }
    ```

12. 赶紧付钱弹框 可以修改配置项 停留时间 5s message消息提示 3s 自动消失

13. Pay组件 methods pay this.message duration 配置时间 复杂写法 时间需求

14. 弹框 留后门 注掉 if(!this.payStatus)

    //后门 i.跳转之前清除定时器 ii.关闭messagebox iii.跳转成功页面
    
    ```js
          // 后门 i.跳转之前清除定时器 ii.关闭messagebox iii.跳转成功页面
          clearInterval(this.timer)
          this.timer = null
          done()
          this.$router.push('/paysuccess')
    ```
    
    