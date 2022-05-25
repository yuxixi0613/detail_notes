## 1. 登录静态页面

1. 直接捞 静态组件 注册&登录 Login Register 至 pages

2. 禅道 管理项目bug 项目代码提交 提测代码

3. 图片路径报错bug icons.png找不到 Login组件 Ctrl+F  查找 icons

   放大镜搜索 只查看.vue icons.png 雪碧图/精灵图

   公共有的 放入 assets Like/images  ListContainer/images icons.png

   ==css中用@符 前面加 ~== 		修改Like .fr 86 修改ListContainer 修改Login

   ```css
   background-image: url(~@/assets/images/icons.png);
   ```

4. 在注册里面找登录(自己写) 在注册里面找登录(已写) 9:50

   将 a 改为 router-link to /login

   ```html
         <h3>
           注册新用户
           <span class="go">
             我有账号，去
             <!-- <a href="login.html" target="_blank">登陆</a> -->
             <router-link to="/login">登录</router-link>
           </span>
         </h3>
   ```

5. 注册 一上来就是交互 收集数据 加验证码

   云片网 手机验证码 发送短信接口 短信验证码

## 2. 收集数据

1. Register组件 data return {

phone:'',

code: '',

password: '',

password2: '',

isChecked:  true

}

2. 对应静态 input 添加 v-model

手机号 v-model phone 验证码 v-model code 密码 v-model password

确认密码v-model password2 	同意协议 v-model isChecked

报错img 验证码img换成button 获取验证码

获取验证码 style width 100px height 38px

```html
<div class="content">
        <label>手机号:</label>
        <input type="text" placeholder="请输入你的手机号" v-model="phone" />
        <span class="error-msg">错误提示信息</span>
      </div>
      <div class="content">
        <label>验证码:</label>
        <input type="text" placeholder="请输入验证码" v-model="code" />
        <button style="width:100px;height:38px;color:white;background-color:#0078d4;" @click="getCode">获取验证码</button>
        <span class="error-msg">错误提示信息</span>
      </div>
      <div class="content">
        <label>登录密码:</label>
        <input type="text" placeholder="请输入你的登录密码" v-model="password" />
        <span class="error-msg">错误提示信息</span>
      </div>
      <div class="content">
        <label>确认密码:</label>
        <input type="text" placeholder="请输入确认密码" v-model="confirmpwd" />
        <span class="error-msg">错误提示信息</span>
      </div>
      <div class="controls">
        <input name="m1" type="checkbox" v-model="isChecked" />
        <span>同意协议并注册《尚品汇用户协议》</span>
        <span class="error-msg">错误提示信息</span>
      </div>
```

## 3. 获取验证码

1. 验证码 img图片换btn并绑定点击事件@click getCode methods定义 发请求获取验证码

2. 写api 请求获取验证码 参数看接口文档 {phone} get 

   reqGetCode  (phone) return request url /user/passport/sendCode/${phone} get

   ```js
   // 请求获取验证码
   export const reqGetCode = (phone) => {
     return request({
       url: `/user/passport/sendCode/${phone}`,
       method: 'get'
     })
   }
   ```

3. vuex三连环 store modules user.js 

   state code:  ''

   mutation RECEIVE_CODE(state,code) state.code = code

   actions async getCode({commit},phone)

   try const result await reqGetCode(phone)

   if result.code 200 commit RECEIVE_CODE result.data

   else alert 获取验证码失败

   catch alert 请求获取验证码失败

   ```js
   const actions = {
     // 获取验证码
     async getCode({commit},phone){
       try {
         const result = await reqGetCode(phone)
         if(result.code === 200){
           commit('RECEIVE_CODE',result.data)
           return result.data
         }else{
           alert('获取验证码失败')
         }
       } catch (error) {
         alert('请求获取验证码失败')
       }
     }
   }
   ```

4. Register methods getCode 发请求获取验证码 

   考虑 手动添加和自动添加 验证码存到 state

   this.$store.dispatch('getCode',this.phone) 避免代码 最好自动添加

   所以 user.js 200 return 'ok' else return Promise.reject(new Error('failed'))

   mutation不写 commit也不写 直接 return result.data

   methods async getCode

   try const result await this.$store.dispatch('getCode',this.phone)

   this.code = result

   catch alert 获取验证码失败

   ```js
     methods: {
       // 发请求获取验证码
       async getCode() {
         try {
           const result = await this.$store.dispatch("getCode", this.phone);
           this.code = result; //拿到返回值验证码直接自动赋值
         } catch (error) {
           alert("获取验证码失败");
         }
       }
     }
   ```

   

## 4. 点击注册

1. 完成注册绑定点击事件@click register methods定义 register 发请求注册

   ```html
   <button @click="register">完成注册</button>
   ```

2. 写api 请求注册 看接口文档 注册 phone password code 存储对象

   reqUserRegister(userInfo)

   return request(

   url /user/passport/register post  data userInfo

   )

   ```js
   // 请求注册
   export const reqUserRegister = (userInfo) => {
     return request({
       url: '/user/passport/register',
       method: 'post',
       data: userInfo
     })
   }
   ```

3. 写vuex三连环 user.js 注册用户

   async userRegister({commit},userInfo)

   try const result = await reqUserRegister(userInfo)

   if result.code 200 return 'ok' 有没有return 看后续有没有其他操作

   else retun Promise.reject(new Error('failed'))

   catch return Promise.reject

   ```js
     // 注册用户
     async reqUserRegister({commit},userInfo){
       try {
         const result = await reqUserRegister(userInfo)
         if(result.code === 200){
           return 'ok'
         }else{
           return Promise.reject(new Error('failed'))
         }
       } catch (error) {
         return Promise.reject(error)
       }
     }
   ```

4. Register组件 点击事件 @click register 发请求注册 

   ```html
     <div class="btn">
       <button @click="register">完成注册</button>
     </div>
   ```

   定义方法 methods  async register

   {

   ​	let {phone,password,password2,code} = this

   ​	if(phone&&password&&password2&&code&&password === password2&&isChecked)  10:54 判断啥意思

   try await this.$store.dispatch('userregister', {phone,password,code})

   alert 注册用户成功 自动跳转到登录页

   this.$route.push('/login')

   catch alert 注册用户失败

   }

   ```js
       // 发请求注册
       async register(){
         let {phone,password,confirmpwd,isChecked} = this
         //基础判断 是否全部都有值 &&并且
         if(phone&&password&&confirmpwd&&code&&passwoed===confirmpwd&&isChecked){
           try{
             await this.$store.dispatch('userRegister',{phone,password,code})
             this.$router.push('/login')
           } catch (error) {
             alert('注册用户失败')
           }
         }
       }
   ```

   >  看接口文档 列步子 写代码 实现需求

5. 测试：注册账号 随便输入账号 后期都用137账号 有后续售后地址

   注册成功 自动跳转 登录页

   if 不写else 后期添加 表单验证

## 5. 登录

1. Login组件 收集数据 data return phone password  完善静态 v-model phone password

   ```js
   export default {
     name: "Login",
     data() {
       return {
         phone: "",
         password: "",
       };
     }
   };
   ```

2. Login组件 登录绑定点击事件@click methods中定义方法 login 发请求 请求登录

   ```html
   <button class="btn" @click="login">登&nbsp;&nbsp;录</button>
   ```

3. 写api接口看接口文档 地址 参数 登录 构造对象userInfo 包含 phone password

   reqUserLogin (unserInfo) return /user/passport/login

   method post data userInfo

   ```js
   // 请求登录
   export const reqUserLogin = (userInfo) => {
     return request({
       url: '/user/passport/login',
       method: 'post',
       data: userInfo
     })
   }
   ```

4. token 登录后标识 登录成功的标志 返回 获取token用户标识

   以前一次请求回来用户请求 现在第一次只返回 token 还需再发一次

5. store user.js state token: '' 获取token 不带用户信息 用户信息需再次请求拿

   mutation RECEIVE_TOKEN(state,token) stae.token = token

   action async userLogin({commit},userInfo) {

   try const result  = await reqUserLogin(userInfo)

   if result.code 200 commit RECEIVE_TOKEN result.data.token

   考虑有没有return 后续有没有其他操作 需要跳转首页

   return 'ok'

   else return Promise.reject(new Error('failed'))

   catch return promise.reject(error)

   }

   ```js
   const state = {
     userTempId:getUserTempId(),  //这个函数就是专门去造用户的唯一标识
     // code: '',
     token: '', //登录后标志就是获取到token，不带用户信息，用户信息是需要携带token再次请求才可拿到
   }
   
   const mutations = {
     // RECEIVE_CODE(state,code){
     //   state.code = code
     // },
   
     RECEIVE_TOKEN(state,token){
       state.token = token
     }
   }
   
     // 登录用户
     async reqUserLogin({commit},userInfo){
       try {
         const result = await reqUserLogin(userInfo)
         if(result.code === 200){
           commit('RECEIVE_TOKEN',result.data.token)
           return 'ok'
         }else{
           return Promise.reject(new Error('failed'))
         }
       } catch (error) {
         return Promise.reject(error)
       }
     }
   ```

6. Login组件 @click.prevent login methods async login(){

   .prevent 阻止默认事件跳转

   ```html
   <button class="btn" @click.prevent="login">登&nbsp;&nbsp;录</button>
   ```

   let {phone,password} = this

   if(phone && password){

   try await this.$store.dispatch('userLogin')

   alert 登录成功，准备自动跳转首页

   this.$route.push('/')

   catch alert 登录失败

   ```js
     methods: {
       async login() {
         let {phone,password} = this
         if (phone && password){
           try {
             await this.$store.dispatch("userLogin", {phone, password})
             alert("登录成功,准备自动跳转到首页")
             this.$router.push("/")
           } catch (error) {
             alert("登录失败")
           }
         }
       }
     }
   ```

7. 测试：登录是否成功 刷新 f12 1370000000 111111

   路径不对 form表单 action ## 跳转 禁用表单

   事件描述符 stop 阻止冒泡 prevent 阻止默认行为

   Login 41H 登录 @click.prevent

8. 再次测试 alert弹框 查看 vuex token拿到没有

## 6. 路由守卫

1. 点击登录 到 Home首页 过程中 发送请求 修改 点击登录注册位置信息

2. 全局前置守卫 匹配路由解析之前去拦 用的最多 管得比较宽 全局

   全局解析守卫  匹配路由解析之中去拦	用得比较少

   全局后置钩子  匹配路由解析之后去拦	用得更少了 几乎用不到就是不用

   用得比较多 路由独享守卫(私人保镖) 拦固定往某个页面跳转得 配置当前路由当中 时间比较靠前

   - 配置在路由当中

   但组件内守卫 拦住固定往某个页面跳转 配置在组件内部 用的比较少

   - 解析完了，已经跳转到组件的时候，但是组件还没创建成功的时候拦截

   只需要记住 全局前置守卫和路由独享的守卫 重要

   

## 7. 根据token获取用户信息 也叫token校验

1. 采用 全局前置守卫 判断是否有 用户信息

2. router index.js 配置全局前置守卫 

   > 实现两个功能 1、根据tokne获取用户信息 2、token校验(过期)

   router.beforeEach(to, form, nex) 

   to 代表目的地路由对象 from 代表出发点路由对象

   next 是一个函数 主要是用来让我们处理使用(放行或者不放行或者去特定其他页面)

   - 参数的不同，处理方式是不一样的

   - nex() 如果不传参代表无条件放行
   - next(false) 传递false代表不符合条件，不放行但是啥也不干 此时页面会卡主
   - next(path或者路由对象) 那么代表不符合条件，去往指定的目的地

   后期用得比较多的 next() 和 next(path或者路由对象)

   router.beforeEach(to, form, nex) 	拦住了必须用 next() 放行

   引入 store

   {

    **1、盲取用户token的用户信息 有没有并不关心 根据token获取用户信息**

   let token = store.state.user.token

   let userInfo = store.state.userInfo

   if(token){

   token存在，代表用户登录过

   if(to.path === '/login'){

   看看用户是不是又去登录，如果又去登录，强制跳转到首页

   next('/') 往这一跳 整个守卫重来再来

   else 用户已经登录而且这次也不是去登录

   对象 转 布尔值 永远为 true

   if(userInfo.name){ 关键判断 有用户信息

   用户登录了也已经获取用户信息了，无敌

   next() 无条件放行 符合所有条件

   }

   else 用户登录了但是没有获取用户信息 请求获取用户信息 目前所处状况

   发请求 去api index.js 根据token获取用户信息 看接口文档 在最下面 新加接口 token校验

   export const reqGetUserInfo request url /user/passport/

   utils request.js 39H 携带用户登录后标识token

   ```js
// 请求根据token获取用户信息
   export const reqGetUserInfo = () => {
    return request({
       url: '/user/passport/auth/getUserInfo',
    method: 'get'
     })
}
   ```

   vuex 三连环 store modules user.js

   state userInfo: {}

   mutation RECEIVE_USERINFO(state,userInfo) state.userInfo=userInfo

   actions async getUserInfo({commit}){

   try const result = await reqGetUserInfo()

   if result.code 200 commit RECEIVE_USERinfo RESULT.data

   成功放行 失败全部归结于token过期

   return  'ok'

   else return Promise.reject(new Error('failed'))

   catch return Promise.reject(error)

   }

   ```js
    RECEIVE_USERINFO(state,userInfo){
       state.userInfo = userInfo
    },
   
   ```

 

```js
 // 根据token获取用户信息
     async getUserInfo({commit}){
    try {
         const result = await reqGetUserInfo()
      if(result.code === 200){
           commit('RECEIVE_USERINFO',result.data)
        return 'ok'
         }else{
        return Promise.reject(new Error('failed'))
         }
    } catch (error) {
         return Promise.reject(error)
    }
     }
```



router index.js  else router.beforeEach(async (to,from,next))

try await store.dispathch('getUserInfo')  next()

catch 认为token过期 对应 token校验 你得删除过期token重新跳转登录页 获取 新token

next('/login')

if(token){}else{

next('/login')  一直跳 守卫逻辑一直循环 卡住了

错误信息 rangeError：maximum call stack size exceeded 栈溢出

next() 目前没登录的时候先让用户无条件跳转，后面我们要回来修改的

有些许问题 没登录 能去支付界面吗 今天主要关心 if里面东西

清空token 目前没写全 后期写全一起清空

写完 页面正常访问 写得没问题 如果写完 页面死了 代表写得有问题

不管 哪个分支里面 if 里面必须要有 next()

测试：f12 vuex 刷新并不会获取用户信息

登录 输入 137000000000 111111 vuex 中发送了 TOKEN USERINFO

user userInfo 用户信息 真真正正拿到用户信息

最后，header里面展示用户信息

登录



### 后面笔记因为 typora 因误删而自动保存无法找回

