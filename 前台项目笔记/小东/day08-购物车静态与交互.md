## 1. 购物车静态界面及修改结构

1. addCartSuccess组件 查看商品详情 a标签换 router-link to /detail/ + skuInfo.id

```html
    <div class="right-gocart">
      <!-- <a href="javascript:" class="sui-btn btn-xlarge">查看商品详情</a> -->
      <router-link to="`/detail/` + skuInfo.id">查看商品详情</router-link>
      <a href="javascript:" >去购物车结算 > </a>
    </div>
```

2. 去购物车结算 shopCar组件 放入 src pages下  router routes 引入注册配置路由

```js
    {
        path: '/shopcart',
        component: ShopCart
    },
```

3. AddcartSuccess a标签 换  router link to /shopCart

```html
    <div class="right-gocart">
      <!-- <a href="javascript:" class="sui-btn btn-xlarge">查看商品详情</a> -->
      <router-link to="`/detail/` + skuInfo.id">查看商品详情</router-link>
      <!-- <a href="javascript:" >去购物车结算 > </a> -->
      <router-link to="/shopcart">去购物车结算 > </router-link>
    </div>
```



4. shopCart 修改静态页面div cart-body ul cart-list 

   依次干掉 3个ul cart-list 中 cart-list-con3  样式干掉 .cart-list-con3

   宽度修正 shopCart 宽度con1 - con 6 百分比   15 35 10 17 10 13

5. ## api 接口请求函数 获取购物车列表  

   reqShopCartList '/cart/cartList' 路径切忌多空格 'get'

   ```js
   // 获取购物车列表
   export const reqShopCartList = () => {
       return request({
           url: `/cart/cartList`,
           method: 'get'
       })
   }
   ```

6. store modules shopCart.js vuex 三连环

   state cartList []  mutations RECEIVE_CARTLIST(state,

   async getShopCartList({commit}) await reqShopCartList

   \== 会出现隐式转换 \==\= 更为严格不会出现隐式转换  ==9:08 补充==

   result.code === 200 commit RECEIVE_CARTLIST result.data

   else alert 获取购物车列表失败

   catch alert 请求获取购物车失败

   ```js
   import { reqAddOrUpdateShopCart, reqShopCartList } from '@/api'
   
   const state = {
       cartList: []
   }
   const mutations = {
       RECEIVE_CARTLIST(state,cartList){
           state.cartList = cartList
       }
   }
   
   /* 
       async函数 异步函数
       - 特点：内部一般都是异步操作
       - async 函数返回值返回promise，不看return
       - 返回的promise是成功还是失败看return
   
       return 结果不一样
       - return的如果是正常值或者不写 返回的promise都是成功的 成功的结果就是return的结果
       - return的如果是失败的promise，那么返回的promise就是失败的，失败的原因就是return的promise失败的原因
       - return的如果是成功的promise，那么返回的promise就是成功的，成功的结果就是return的promise成功的结果
       - 如果抛出异常，那么返回的promise就是失败的，失败的原因就是抛出的异常原因
   
   */
   
   // 什么时候action函数当中要写return
   // 当我们dispatch后，需要根据请求的成功和失败有后续操作，就要写return
   const actions = {
   
       // 添加购物车或者修改购物车数量
       async addOrUpdateShopCart({commit},{skuId,skuNum}){
           try {
               const result = await reqAddOrUpdateShopCart(skuId,skuNum)
               if(result.code === 200){
                   return 'ok'
               }else{
                   return Promise.reject('failed')
               }
           } catch (error) {
               // 失败的promise可以让作用域链继续往下传
               return Promise.reject('failed')
           }
       },
   
       // 获取购物车列表数据
       async getShopCartList({commit}){
           try {
               const result = await reqShopCartList()
               if(result.code === 200){
                   commit('RECEIVE_CARTLIST',result.data)
               }else{
                   alert('获取购物车列表失败')
               }
           } catch (error) {
               alert('请求获取购物车列表失败')
           }
       },
   
       
   }
   const getters = {}
   
   export default {
       state,
       mutations,
       actions,
       getters
   }
   ```

7. ShopCart组件 mounted 发请求 this.getShopCartList methods this.$store.dispatch()

   ```js
     export default {
       name: 'ShopCart',
       mounted() {
         this.getShopCarList();
       },
       methods: {
         getShopCarList() {
           this.$store.dispatch("getShopCartList")
         },
       }
     }
   ```

8.  测试：添加商品 修改数量 添加购物车 去购物车结算

   Vuex RECEIVE_CARTLIST 看数据 shopcart cartList

   Network 看请求 请求成功 [] 无数据

9. ## 无登录状态 无法记录用户行为 添加购物车 去购物车结算

   http请求 无状态请求 无用户标识符区分不同用户

   无状态请求：根源http协议 无状态协议 

   1次 2次请求发送给后台 均被识别为请求 不识别用户

   所以引入了 cookie 与 session 保持状态请求 记录用户行为

   cookie 客户端     ==9:30补充==

   session 服务器    sessionkey sessionValue expiration time

   sessionkey 随机字符串 sessionValue 用户id相关信息 expiration time 过期时间

   session 安全性比较高 网络之间流通的只是 随机字符串

   session cookie 现在没人用 比较low 落后 于是出现了 Token

   添加购物车的时候 添加标识：和后端配合 添加用户标识

10. store modules user 添加用户 临时的标识 唯一不变

    state userTempId getUserTempId() 专门造用户唯一标识

    用户临时Id标识存储state 效率高 拿数据比较快 点击请求 state全部初始化

11. utils 新建userabout.js function getUserTempId uuid

    创建一个唯一用户标识 UUID NanoID

    UUID：https://www.npmjs.com/package/uuid

    uuid不用npm下 它和lodash一样，其他包依赖它 脚手架自带

    唯一 一个用户只有一个不能重复，轻易不能变 localstorage

    看看有没有 如果有 就不需要再给用户造新的

    let userTempId localStorage.getItem('userTempId_key')

    if !userTempId userTempId = uuidv4() localStorage.setItem('userTemp_key',userTempId) 

    return userTempId

    export default getUserTempId

    ```js
    // 这个函数就是专门给用户制作唯一标识
    import {
      v4 as uuidv4
    } from 'uuid'
    
    function getUserTempId() {
      // 唯一 一个用户只有一个不能重复，轻易不能变
      // 看看有没有,如果有,就不需要再给用户造新的
      let userTempId = localStorage.getItem('userTempId_key')
      if(!userTempId){
        // 如果没有，那么久造一个新的，并且存储localStorage
        userTempId = uuidv4();
        localStorage.setItem('userTempId_key',userTempId)
      }
      return userTempId
    }
    
    export default getUserTempId
    ```

12. store modules user.js import getUserTempId

    ```js
    import getUserTempId from '@/utils/userabout'
    ```

13. 每一次请求都要带上用户唯一标识 拦截器

    utils request.js 引入store 请求拦截器 config.headers

    let userTempId = store.state.userTempId/localStorage.getItem('user')

    localStorage.getItem('user')也行，但不好 效率低慢 store中快得多

    Vuex获取效率比较高 初始化取一次 快得多

    if(userTempId) config.headers.userTempId = userTempId

    测试：重新 添加商品 修改数量 添加购物车 去购物车结算

14. Vuex RECEIVE_CARTLIST 看数据 shopcart cartInfoList

15. 实现 未登录状态 状态保持 购物车支付   ==补充10:10==

16. ==面试亮点 购物车支付==   添加用户临时唯一标识

17. 渲染数据展示 直接展示数据 需要计算展示数据

18. shopcart cartList 无购物券 有购物券

19. ShopCart组件 获取数据 computed ...mapState

20. 两层遍历数据 cart-body vfor cart cartList :key index

21. cart-list vfor cartInfo cart.cartInfoList :key cartInfo.id

22. input cart-list-con1 :checked "cartInfo.isChecked"

23. cart-list-con2 img :src "cartInfo.imgUrl" cartInfo.skuName cart.skuPrice

24. input :value cartInfo.skuNum

25. 需要计算的属性 全选 已选 总价

26. 已选择几件商品 打钩各商品数量合

27. 计算选择的商品数量 统计必然是 reduce 双层数组高阶方法 外层看作遍历 搞内层

28. 高阶函数：参数或者返回值是函数的函数叫高阶方法或高阶函数

29. computed checkedNum return this.cartList.reduce((prev, item) => {item.cartInfoList.reduce((prave1,item1)=>{if(item1.isCheed){preve1 += item1.skuNum}return prev1},0) return prev}, 0)

30. 已选择 {{check}}

31. 总价 shopCart组件 allMoney(){}

32. 全选   11:20

33. 单个CheckBox v-model收集的是布尔值 这个值最终作用的就是checked

34. 成组的CheckBox v-model  收集的是数组

35. v-model 和 checked 点击事件

36. cn.vuejs.org/v2/guide/forms/html

37. check + click 读写  9:05

    v-model + get/set 更适合vue

38. v-model isCheckAll:{

39. get(){

40. 一项没选 整体没选

41. ​	return this.cartList.every(item => {

42. ​	return item.cartInfoList.every(item1 => {

43. ​	return item1.isChecked

44. }

45. })

46. },

47. 简化 return this.cartList.every(item => item.cartInfoList

48. set(){

49. }

50. }

51. 单个使用多选框其实有两套方法

52. 1. 采用v-model配合计算属性(get和set)
    2. 采用check属性值绑  ==11:30==

53. 修改数量 修改状态 删除(单个删除，多个删除) 上市公司 资本就注入

54. 

## 2. 购物车交互    

> a. 修改数量 b. 修改状态 c. 删除(单个删除，多个删除)

## 修正量 修改购物车商品数量

1. shopCart组件 @click changeNum() -1 1 cartInfo
2. @change changeNum($event.target.value - cartInfo.skuInfo)

3. methods 点击修改购物车商品数量 changeNum(cartInfo, disNum)

4. 判断最终的量是不是小于1 如果小于1就让最终的量等于1

5. 变化的量 就应该修正为 1 - 原本有的量

6. if(cartInfo.skuNum + disNum < 1) disNum = 1 - cartInfo.skuNum

7. shopCart组件  async changeNum 发请求 
8. try this.$store.dispatch('addOrUpdateShopCart', {skuIdId:cartInfo.skuId,skuNum:disNum}) alert 修改购物车数量成功 this.getShkoCartList()

9. catch alert 修改购物车数量失败

10. 测试：去购物车结算 点击+-或输入 数量 进行 商品数量修改操作

## 单选全选 修改购物车单选全选状态

1. ShopCart组件 li class="cart-list-con1"处 @click updateOneIsChecked(cartInfo)

2. methods 点击修改单个的选中状态 updateOneIsChecked(cartInfo)

3. api 接口请求函数 修改单个的选中状态

4. export const reqUpdateOneIsChecked (skuId,isChecked)

5. return request url /cart/checkcart/\${skuId}\$

6. store modules shopcart.js 修改购物车单个的选中状态

7. async updateOneIsChecked {commit},{skuId,isChecked}

8. try result await reqUpdateOneIsChecked(skuId,isChecked)

9. 14:32 一般return错误对象 以免后面继续 点操作符

10. if result.code === 200  return "ok" else return Promise.reject(new Error(('failed')

11. catch return Promise.reject(error)

## 修改单选

12. ShopCart组件 methods updateOneIsChecked 续写

13. try this.$store.dispatch("updateOneIsChecked", {

14. skuId: cartInfo.skuId,

15. 单选取反 isChecked: cartInfo.isChecked ? 0 : 1,

16. });

17. alert 修改单车状态成功

18. this.getShopCartList

19. catch alert 修改单车状态失败

20. 测试：走一步 侧一步 单选 反选 全选为计算出来的

21. api接口文档 最后 批量选中购物车接口 {isChecked}

22. api index.js 接口请求函数 修改多个购物车选中状态

23. export const reqUpdateAllIsChecked (skuIdList,isChecked)

24. return request ({

25. url: /cart/batchCheckCart/\${isChecked} methods: 'post' data: skuIdList

26. })

27. vuex 三连环 store modules shopcart.js 批量修改购物车的选中状态

28. async updateAllIsChecked {commit},{skuIdList,Ischecked}

29. try result await reqUpdateAllIsChecked(skuIdList,isChecked)

30. if result.code 200 return 'ok'

31. else return Promise.reject(new Error('faild))

32. catch return Promise.reject(error)

33. ShopCart组件 197H set(val) 先准备两个参数

34. val拿的是你修改后的最新值 这个值一会给你上面全部要改得值

35. {let isChecked = val ? 1 : 0

36. let skuIdList = []

37. 第一种 forEach if判断提升效率 判断已打钩的 只去打钩未选的 不用重新全部打钩

38. this.cartList.forEach(item => {

39. ​	item.cartInfoList.forEach(item1 => {

40. ​		if(item1.isChecked !== isChecked){

41. ​			skuIdList.push(item1.skuId)

42. }

44. 测试：console.log(skuIdList) F12 点击全选

45. 第二种 reduce 工作中常用 高阶方法常用 比forEach效率高

46. concat 合并数组 将里面数组合并至调用的数组返回新数组

47. skuIdList = this.cartList.reduce((prev,item) => {

48. prev = prev.concat (item.cartInfoList.reduce((prev1,item1) => {

49. ​		if(item1.isChecked !== isChecked){

50. ​			prev1.push(item1.skuId)

51. ​		}

52. ​		return prev1

53. ​	}, []))

54. ​	return prev

55. }, [])

56. 

57. 测试：console.log(skuIdList)

58. 此处 await必须加 不然点击全选按钮不等待 异步延迟加载可能出现问题

59. try await this.$store.dispatch('updateAllIsChecked',{skuIdList,isChecked})

60. alert 修改所有的购物车状态成功

61. this.getShopCartList()

62. catch alert 修改所有的购物车状态失败

## 删除(单个删除，多个删除)

1. ShopCart组件 64H javascript:; @click 
2. methods 点击删除单个购物车 

3. api index.js 删除单个购物车 接口文档 删除购物车商品  {skuId} DELETE

4. export const reqDeleteOneCart (skuId) 

5. return request url  method: detele

6. store module shopcart.js 删除单个购物车
7. async deleteOneCart {commit},skuId

8. try reqDeleteOneCart(skuId)

9. if result.code === 200 return 'ok'

10. else 

11. 续写 methods deleteOneCart(cartInfo)
12. try await this.$store.dispatch('deleteOneCart',cartInfo.skuId)
13. alert 删除购物车成功

14. this.getShopCartList

15. catch 

16. shopCart组件 82H 删除选中的商品 @click deleteCheckedCart javascript:;
17. methods 点击删除选中的购物车 deleteCheckedCart 发请求 写api接口请求函数

18. api index.js export const reqDeleteCheckedCart = (skuIdList)

19. {return request({ url /cart/batchDeleteCart, method: 'delete' data: skuIdList})}

20. store shopcart.js Vuex 三连环 删除选中的购物车
21. async deleteCheckedCart {commit}, skuIdList

22. try result await reqDeleteCheckedCart(skuIdList)

23. if result.code === 200 return 'ok'

24. else return Promise.reject(new Error('failed')

25. 续写 methods deleteCheckedCart 
26. let skuIdList = []

27. this.cartList.forEach((item) => {

28. ​	itme.cartInfoList.forEach((item1) => {

29. ​		if(item1.isChecked){

30. ​			skuIdList.push(item1.skuId)

31. try await this.$store.dispatch 

32. 全选 勾选未取消 isCheckAll get() && length !== 0