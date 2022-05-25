





[TOC]

# 1.版本问题

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



# 2.关闭eslint语法检查

> 在vue.config.js里配置
>
> ```js
> lintOnSave: false, //关闭eslint语法检查
> ```



------



# 3.配置代理（配置看门狗，让看门狗生效）

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

# 4.jsconfig.json配置别名@提示

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

# 5.Router/index.js报错

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



# 6.解决编程式导航多次点击参数不变抛出的NavigationDuplicated异常（二次封装）

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

# 7.中断promise链

返回pending状态的promise

```js
return new Promise(() => {});
```





------

# 8.思想

#### 排他思想

1.干掉所有人

2.再成就自己

#### 计数器思想

#### 开关思想

#### 参考值思想

定义一个值去参考一个值

#### 占位思想

------

# 9.事件回调与事件对象

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

# 10.自定义属性添加标识

HTML5写法：

data-xxx = ""

自定义属性 xxx="{}"

------

# 11.拦截器

**做两件事：**

>    1、修改报文（配置项）
>
>    2、添加额外的功能



------

# 12.发请求

工具utils

api接口请求函数

vuex存储

三连环



------

# 13.优化typeNav数据ajax请求次数，改变请求的位置

之前我们是在typeNav组件内部dispatch去发送ajax请求，这样的话

因为typeNav是被多个页面公用的，所以每次切换到一个页面，这个组件都会重新创建  mounted都会执行

因此有几个页面公用了这个typeNav就会执行几次ajax请求

所以我们放到App里面就只用执行一次，因为数据一样，没必要多次请求



------

# 14.dispatch发送请求从mounted（只能发送第一次，后续dispatch发送请求需要用watch监视路由变化）+watch配合使用，进阶为直接在watch里dispatch加上immediate配置项

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

# 15.节流与防抖

#### 正常：

事件触发非常频繁，而且每一次的触发，回调函数都要去执行（如果时间很短，而回调函数内部有计算，那么很可能出现浏览器卡顿）

#### 节流：

在规定的间隔时间范围内不会重复触发回调，只有大于这个时间间隔才会触发回调，把频繁触发变为少量触发

#### 防抖：

前面的所有的触发都被取消，最后一次执行在规定的时间之后才会，也就是说如果连续快速的触发  只会执行一次



------

# 16.路由传参相关

指定params参数时可不可以用path和params配置的组合?（对象写法）

1. 不可以用path和params配置的组合, 只能用name和params配置的组合
2. query配置可以与path或name进行组合使用



------

# 17.分页器组件

#### 四个数据

当前页

总条数

总页

连续页



------

# 18.Getters里计算的属性return时需要||一个空{}



原则：拿数据的时候不能为undefined

解决方案：

1、出现这种假报错，就计算。谁可能是undefined就加 || {}  、  || []   

2、v-if

**原理：**

初始化state的时候为空，

getters里计算拿到的是undefined，computed拿的时候也为undefined，

报错但页面正常，发请求，请求回来了，getters重新走，数据改变，重新计算，computed重新拿数据，响应式数据重新渲染

------

# 19.添加标识实现未登录操作购物车









------

# 20.测试数据是否拿到：

> 先请求获取数据到vuex，测试vuex当中数据存在不，不存在查network，看看请求发了没有
>
> 请求如果发了，看三连环，如果请求没发，看你的接口请求函数
>
> 如果vuex存在，就获取vuex的数据到vue，测试vue当中数据存在不，不存在，查看获取的时候代码
>
> 不熟悉的时候，走一步测一步


​	

> 有些人  从写api 一直把代码都写到获取数据到组件当中，中间没测试过
>
> 1. ​	先看vue当中有没有数据  没有数据，再去看vuex里面有没有
> 2. ​	如果vuex里面有，检查从vuex获取数据是不是失败
> 3. ​	如果vuex没有，先看network请求发了没有
> 4. ​	如果请求没发，看接口请求函数有没有问题
> 5. ​	如果请求发了，看vuex三连环是不是有问题

------

# 21.配置路由滚动行为

1. [Vue Router v3.x 滚动行为 异步滚动]: https://v3.router.vuejs.org/zh/guide/advanced/scroll-behavior.html#%E5%BC%82%E6%AD%A5%E6%BB%9A%E5%8A%A8	"跳转后垂直滚动条不置顶问题"

   配置路由的固定项

   ```js
   export default new VueRouter({
       routes,
       // 下面这个函数以后是配置路由的固定项
       scrollBehavior (to, from, savedPosition) {
           return { x: 0, y: 0 }
       }
   })
   ```

2. 再次测试：走你



------

# 22.商品详情页跳转的详细思路

```js
在search点击一个商品链接，路由跳转，携带params参数在路径
<router-link :to="'/detail/' + goods.id">{{ goods.title }}</router-link>

路由跳转到detail

初始化skuId参数
data() {
    return {
      skuId: "",
    };
},
    
挂载前：将路由中的params参数
beforeMount() {
    this.skuId = this.$route.params.skuId;
},
    
挂载后调用methods中定义的getGoodsDetailInfo方法，dispatch派发vuex中的actions中的"getGoodsDetailInfo",并携带在挂载前已赋值的参数skuId
mounted() {
    this.getGoodsDetailInfo();
},
方法：
methods: {
    getGoodsDetailInfo() {
      this.$store.dispatch("getGoodsDetailInfo", this.skuId);
    },
},

进入到actions中的getGoodsDetailInfo：
上面的实参this.skuId 传到下面的形参skuId
async getGoodsDetailInfo({ commit }, skuId) {
    try {
        进入到api中的reqGoodsDetailInfo，并将skuId参数传给api的接口请求函数  ⨀
        const result = await reqGoodsDetailInfo(skuId)
        if (result.code === 200) {
            commit('RECEIVE_GOODSDETAILINFO', result.data)
        } else {
            alert('获取商品详情失败')
        }
    } catch (error) {
        alert('请求获取商品详情失败')
    }
}

在api中的reqGoodsDetailInfo接口请求函数：
下面的形参skuId来自 33行 ⨀
export const reqGoodsDetailInfo = (skuId) => {
接下来走utils中的request，即封装好的axios，发送请求，返回一个promise对象
    return request({
        url: `/item/${skuId}`,
        method:'get',
    })
}

在store的detail模块中书写三连环
在state中初始化将要请求过来的数据
const state = {
    goodsDetailInfo: {}
}

const actions = {
	async getGoodsDetailInfo({ commit }, skuId) {
    	try {
        	const result = await reqGoodsDetailInfo(skuId)
若请求返回的数据的状态码code为200
        	if (result.code === 200) {
携带返回的数据中的data 即 result.data 提交给mutations中的'RECEIVE_GOODSDETAILINFO'处理
            	commit('RECEIVE_GOODSDETAILINFO', result.data)
        	} else {
            	alert('获取商品详情失败')
        	}
    	} catch (error) {
        	alert('请求获取商品详情失败')
    	}
	}
}

const mutations = {
    参数 state 为三连环中的state，形参goodsDetailInfo接收的是上面的result.data的实参
    将请求回来的数据赋值给vuex中的state.goodsDetailInfo给组件共享数据
    RECEIVE_GOODSDETAILINFO(state, goodsDetailInfo) {
        state.goodsDetailInfo = goodsDetailInfo
    }
}

在组件中的computed里通过...mapstate取数据



```



------

# 22.async函数  

是一个异步函数

特点：内部一般都是异步操作

async 函数返回值返回promise,不看return

返回的promise是成功还是失败看return



return结果不一样

return的如果是正常值或者不写 返回的promise都是成功的 成功的结果就是return的结果

return的如果是失败的promise，那么返回的promise就是失败的，失败的原因就是return的promise失败的原因

return的如果是成功的promise，那么返回的promise就是成功的，成功的结果就是return的promise成功的结果

如果抛出异常，那么返回的promise就是失败的，失败的原因就是抛出的异常原因



------

# 23.什么时候action函数当中要写return



当我们dispactch后，需要根据请求的成功和失败有后续操作，就要写return







------

# 24.在css中路径用@/需要加~

示例：

```css
background: url(~@/assets/images/icons.png) no-repeat -10px -201px;
```







------

# 25.四种常见错误Error

type

reference

range

sync



#### 报错201

前端请求参数有问题

------

# 26.userTempId和token的区别

userTempId前端做的

token后端做的

```js
	userTempId  未登录状态下的用户身份识别标识

	token       登录状态下的用户身份识别标识 

	如果没登陆，请求头当中只带了临时标识，添加的购物车信息是和临时身份标识对应的信息

	如果登录了，那么我们同时在请求头添加临时标识和登录后标识，
		那么此时后台会把临时标识对应的数据，转移到真正登录的标识数据里面，而临时标识对应的数据就不见了
```


```js
	两个标识都存在的话，后台会合并临时id对应的信息到token对应的信息上 token是老大
```



------

# 27.element-ui气泡确认框的事件 之 官方坑



气泡确认框点击确定按钮的事件名称叫 onConfirm 不是confirm  （经源码确认）官网写错了





------

# 28. vue elementUI tree树形控件获取父节点ID的实例

> 原本是只带了一个父级id
>
> 需要修改为带上从一级到n级的id
>
> 
>
> 分配权限
> 					需要修改源码，获取所有的上级id，否则权限添加不上



## 解决-修改源码:

###     情况1: element-ui没有实现按需引入打包

> ​     node_modules\element-ui\lib\element-ui.common.js   25382行修改源码  去掉 'includeHalfChecked &&'
>
> ​     // if ((child.checked || includeHalfChecked && child.indeterminate) && (!leafOnly || leafOnly && child.isLeaf)) {
>
> ​     if ((child.checked || child.indeterminate) && (!leafOnly || leafOnly && child.isLeaf)) {
>

###     情况2: element-ui实现了按需引入打包

> ​     node_modules\element-ui\lib\tree.js   1051行修改源码  去掉 'includeHalfChecked &&'
>
> ​     // if ((child.checked || includeHalfChecked && child.indeterminate) && (!leafOnly || leafOnly && child.isLeaf)) {
>
> ​     if ((child.checked || child.indeterminate) && (!leafOnly || leafOnly && child.isLeaf)) {







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

this.$bus.$emit是调用绑定在全局事件总线上的事件

this.$emit是调用自定义事件





------









