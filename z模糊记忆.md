[TOC]

# 数组

## **功能分类**：

### 1. 数组的**创建**

```js
var arrayObj = new Array();　//创建一个数组
var arrayObj = new Array([size]);　//创建一个数组并指定长度，注意不是上限，是长度
var arrayObj = new Array([element0[, element1[, ...[, elementN]]]]);　//创建一个数组并赋值
```

要说明的是，虽然第二种方法创建数组指定了长度，但实际上所有情况下数组都是变长的，也就是说即使指定了长度为5，仍然可以将元素存储在规定长度以外的，注意：这时长度会随之改变

### 2. 数组的元素的**访问**

```js
var testGetArrValue=arrayObj[1]; //获取数组的元素值
arrayObj[1]= "这是新值"; //给数组元素赋予新的值
```

### 3. 数组元素的**添加**

```js
arrayObj.push([item1 [item2 [. . . [itemN ]]]]);
// 将一个或多个新元素添加到数组结尾，并返回数组新长度

arrayObj.unshift([item1 [item2 [. . . [itemN ]]]]);
// 将一个或多个新元素添加到数组开始，数组中的元素自动后移，返回数组新长度

arrayObj.splice(insertPos,0,[item1[, item2[, . . . [,itemN]]]]);
//将一个或多个新元素插入到数组的指定位置，插入位置的元素自动后移，返回""。
```

### 4. 数组元素的**删除**

```js
arrayObj.pop(); //移除最后一个元素并返回该元素值

arrayObj.shift(); //移除最前一个元素并返回该元素值，数组中元素自动前移

arrayObj.splice(deletePos,deleteCount);
//删除从指定位置deletePos开始的指定数量deleteCount的元素，数组形式返回所移除的元素
```

### 5. 数组的**截取和合并**

```js
arrayObj.slice(start, [end]);	前闭后开
//以数组的形式返回数组的一部分，注意不包括 end 对应的元素，如果省略 end 将复制 start 之后的所有元素

arrayObj.concat([item1[, item2[, . . . [,itemN]]]]);
//将多个数组（也可以是字符串，或者是数组和字符串的混合）连接为一个数组，返回连接好的新的数组
```

### 6. 数组的**拷贝**

```js
arrayObj.slice(0); //返回数组的拷贝数组，注意是一个新的数组，不是指向
arrayObj.concat(); //返回数组的拷贝数组，注意是一个新的数组，不是指向
```

### 7. 数组元素的**排序**

```js
arrayObj.reverse(); //反转元素（最前的排到最后、最后的排到最前），返回数组地址
arrayObj.sort(); //对数组元素排序，返回数组地址
```

### 8. 数组元素的**字符串化**

`join()`方法是一个非常实用的方法，它把当前Array的每个元素都用指定的字符串连接起来，然后返回连接后的字符串：

```js
arrayObj.join(separator); //返回字符串，这个字符串将数组的每一个元素值连接在一起，中间用 separator 隔开。

var arr = ['A', 'B', 'C', 1, 2, 3];
arr.join('-'); // 'A-B-C-1-2-3'
```



如果Array的元素不是字符串，将自动转换为字符串后再连接。

`valueOf`: 与String类似，Array也可以通过indexOf()来搜索一个指定的元素的位置：

```js
var arr = [10, 20, '30', 'xyz'];
arr.indexOf(10); // 元素10的索引为0
arr.indexOf(30); // 元素30没有找到，返回-1
arr.indexOf('30'); // 元素'30'的索引为2
```



toLocaleString 、toString：可以看作是join的特殊用法，不常用;
**toLocaleString()**: 方法可根据本地时间把 Date 对象转换为字符串，并返回结果。

**toString**:把数组转换为字符串，并返回结果。

**lastIndexOf**：返回在数组中搜索到的与给定参数相等的元素的最后（最大）索引。

**toSource()**: 返回一个字符串,代表该数组的源代码.该特性是非标准的，请尽量不要在生产环境中使用它！ 

### 9. 求两个数组的交集、差集和并集？

```js
// 求交集（a ∩ b）两种方法
let intersection = a.filter(v => b.includes(v))

let intersectionSet = Array.from(new Set([...a].filter(x => b.includes(x))))

// 求差集（a - b）两种方法
let difference = a.concat(b).filter(v => !a.includes(v) || !b.includes(v))

let differenceSet = Array.from(new Set([...a].filter(x => !b.includes(x))))

// 求并集（a u b)两种方法
let union ＝ a.concat(b).filter(function(item, index, array) {
  return array.indexOf(item) === index;
})

let unionSet = Array.from(new Set([...a, ...b]))
```

### 10.数组**扁平化**-reduce

```js
var flattened = [[0, 1], [2, 3], [4, 5]].reduce((a, b) => {
    return a.concat(b);
});

ES 10 API：
arr.flat(深度)  深度：1、 2、…… Infinity
// flattened is [0, 1, 2, 3, 4, 5]
```

## **会改变原数组**：

pop、push、shift、unshift

splice、sort、reverse

##### 在ES6总会改变原数组:	

copyWithin()	指定位置的成员复制到其他位置

fill()	填充数组



## **不会改变原数组**：



slice、join、toString、concat、indexOf、lastIndexOf、includes

##### 这些迭代方法不会改变原数组

map、filter、some、every、reduce、forEach



## **伪数组转真数组**

```js
// ES5 的写法
var arr1 = [].slice.call(arrayLike); // ['a', 'b', 'c']

// ES6 的写法
let arr2 = Array.from(arrayLike); // ['a', 'b', 'c']
```



## **判断是否为数组**

`typrof`难以胜任

```js
var arr=new Array("1","2","3","4","5");
alert(typeof(arr));  // Object
```



`instanceof`可以胜任，但在多个frame总穿梭就会出现问题

```js
var arrayStr=new Array("1","2","3","4","5");
alert(arrayStr instanceof Array);  //true
```



`isArray` 优于` instanceof`,因为Array.isArray能检测`iframes`.

```js
// 鲜为人知的事实：其实 Array.prototype 也是一个数组。
Array.isArray(Array.prototype);
```



`toString`

```js
function isArray(obj) {  
  return Object.prototype.toString.call(obj) === '[object Array]';   
}
```



当检测Array实例时, `Array.isArray` 优于 `instanceof`,因为Array.isArray能检测`iframes`.



## **几个注意点**：

> 1. shift,pop 会返回那个被删除的元素
> 2. splice 会返回被删除元素组成的数组，或者为空数组
> 3. push 会返回新数组长度
> 4. some 在有true的时候停止
> 5. every 在有false的时候停止
> 6. 上述的迭代方法可以在最后追加一个参数 thisArg,它是执行 callback 时的 this 值。



------

# Math处理小数、随机数、幂、最值、三角函数

##### 处理小数

```js
Math.floor	//地板
Math.round	//四舍五入
Math.ceil	//天花板
```

##### 随机数

```js
Math.randow	//随机数
```

##### 幂

```js
Math.pow	//幂运算

//ES6幂运算的新语法
**
    示例：2**3		即为：2的3次方
```

##### 最值

```js
Math.max(...[])	//数组最大值
Math.min(...[])	//数组最小值
```

##### 三角函数

```js
Math.sin
Math.cos
Math.tan
```



------

# ES6模块化



> 无论什么暴力方式，最终暴露是一个对象，只是对象形成的方式不一样



### 默认暴露和引入

以default为key，以export default 后面的值为value的一个对象



```js
真实完整引入写法
import {default as a} from './xxx'

简写
import a from './xxx'
```







### 分别暴露

统一暴露形成的对象，是系统给你形成的花括号



```js

import {a,b} from './xxx'

```









### 统一暴露

统一暴露形成的对象，是自己形成的花括号，可以认为是分别暴露的简写

```js
const a = 100
const b = [1,2,3]

export {
	a,
	b,
}
```



```js

import * as xxx from './xxx'

```



### ！引入并暴露



```js
引入并暴露
即先引入再暴露

可以这么理解，但是是错误写法
import trademark from './trademark'	简写
import {default as trademark} from './trademark'	全写
export trademark


```



```js
！！！默认暴露的时候不能简写
export {default as trademark} from './trademark'
export {default as attr} from './attr'

下面 user.js 中是分别暴露的，可以简写
export * as user from './user'
```



```js
最终暴露出 index.js 的对象是这样的结构
{
    trademark:{
        remove
    }
    user:{
        login
    }
}

```



------

# 拷贝

```js
1.什么是拷贝？
拷贝说的是数据拷贝，拷贝一定会出现新的内存空间

2.深拷贝和浅拷贝
深拷贝和浅拷贝针对的都是对象数据，对象数据当中的属性是对象数据，此时才会区分深浅拷贝

浅拷贝：
	当拷贝一个对象时，对象内部的对象数据，地址值是一样的，属于浅拷贝
	若对象内部不存在对象数据，那么浅拷贝就叫拷贝，拷贝其实说的就是外部的对象
    浅拷贝后的地址*一样*，改变浅拷贝的新数据会影响原数据

深拷贝：
	当拷贝一个对象时，对象内部的对象数据，地址值是*不*一样的，属于深拷贝
	若对象内部不存在对象数据，那么深拷贝就叫拷贝，拷贝其实说的就是外部的对象
    深拷贝后的地址*不一样*，改变深拷贝的新数据*不会*影响原数据

3.如何选择深拷贝还是浅拷贝？
看拷贝的对象内部有没有对象，若有则深拷贝，若无则浅拷贝
```



------

# v-if与v-show





------

# 事件修饰符

.native

.stop

.prevent

.once



------

# async函数 

异步函数

特点：内部一般都是异步操作

async 函数返回值返回promise，不看return

返回的promise是成功还是失败看return

```js
return 结果不一样
- return的如果是正常值或者不写 返回的promise都是成功的 成功的结果就是return的结果
- return的如果是失败的promise，那么返回的promise就是失败的，失败的原因就是return的promise失败的原因
- return的如果是成功的promise，那么返回的promise就是成功的，成功的结果就是return的promise成功的结果
- 如果抛出异常，那么返回的promise就是失败的，失败的原因就是抛出的异常原因
```

------

# v-for与v-if同时使用时效率的问题

v-for优先级更高

v-if指令需要多次解析，因此效率低下，官方不建议使用

解决方案：计算出需要遍历的数组，不要使用v-if



 v-for和v-if同时使用，谁的优先级高

 v-for优先级高，但是官方建议能不这样写就不这样写，因为效率低（v-if指令需要多次解析）

```vue
<button v-for="page in startEnd.end" :key="page" v-if="page >= startEnd.start">{{page}}</button>
```

解决方案：计算出需要遍历的数组，不要使用v-if 

------

# vuex

### ...mapstate()

取数据在computed中使用...mapState()

```js
computed: {
    //万能写法：
    ...mapState({
        yyy: (state) => state.xxx
    }),
}

或  ...mapState({miaoshu:'description',he:'sum'})

或  ...mapState(['description','sum'])
```

> **注：**此处的state是...mapState方法内置的形参，即vuex里的state



### ...mapGetters()

计算数据（使得数据操作简化）在computed中使用...mapGetters

```js

computed: {
    //（第一种写法,对象式）靠mapGetters读取vuex中getters里的bigSum
    ...mapGetters({daHe:'bigSum'})

    //（第二种写法,数组式）靠mapGetters读取vuex中getters里的bigSum
    ...mapGetters(['bigSum'])
},
    
```





![image-20220422090258121](H:\前端typora笔记\typora-user-images\image-20220422090258121-16505893887311.png)



### state

### mutations

### actions

### getters



### modules

在大store（index.js）里配置，里面配小store的模块文件对象

```js
引入
import Vue from 'vue'
import Vuex from 'vuex'
import getters from './getters'
import app from './modules/app'
import settings from './modules/settings'
import user from './modules/user'

创建配置
const store = new Vuex.Store({
	modules: {
    	app,
    	settings,
    	user
	},
  	getters
})

暴露
export default store
```



### namespaced命名空间

```js

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





------

# 使用vuex与不适用vuex的区别差异



### 使用vuex时

api

三连环

dispatch

数据存在vuex中



### 不使用vuex时

api

挂api在vue的原型上

组件`this.$API.xxx`调用api中的接口请求函数

数据存在组件data



------

# 自定义事件与原生事件的$event

#### 自定义事件中的$event

是默认参数，即传递过来的数据

**注意：**

> 1. 不使用圆括号，只能传入一个参数即**$event**
>2. 若想传递多个数据，则把多个数据包装成对象、数组的方式作为一个参数去传递



#### 原生事件中的$event

是事件对象

**注意两点:**

> 1. 不使用圆括号，这个情况一般是没有别的参数时候，event事件对象会被自动当作实参传入
> 2. 使用圆括号，必须显式的传入`$event`事件对象，如果不传入可能最终找到的是全局的window .event
> 2. 使用圆括号，显示传入`$event`事件对象，后面可以传入多个参数

------

# 插槽

### 默认插槽和具名插槽

```js
一个template对应一个插槽，每个template都要写v-slot，代表给哪个插槽填坑

如果是默认插槽
v-slot:default
默认插槽只能有一个

具名插槽
<slot name=""></slot>
v-slot:aa

```



### 作用域插槽

```js

1.数据一定是定义或初始化在父组件中
2.数据要传递给子组件，让子组件去展示使用
3.子组件在展示的过程中，数据的结构子组件说了不算，由父组件决定
```





------

# sync



```js
父组件给子组件属性传递数据后面添加.sync子组件修改数据 

需要分发事件 @click = $emit("update:属性名",要更新的数据) 
```



------

# axios传参与路由传参

## axios传参

#### params参数

params参数只能在路径当中去写    /user/1

#### query参数

有两个地方可以写：

1、路径当中以?分割 问号后面的  key=value&key=value

2、在配置项的params里面写query参数

#### data参数（请求体参数）

一般是post请求、put请求携带的比较多，只能写在data配置项中



## 路由传参

#### params参数

#### query参数

------

# axios的对象式写法

**axios对象式写法需要注意的是：不同请求方式的参数结构有所不同**

> ##### axios.request(config)
>
> ##### axios.get(url[, config])
>
> ##### axios.delete(url[, config])
>
> ##### axios.head(url[, config])
>
> ##### axios.options(url[, config])
>
> ##### axios.post(url[, data[, config]])
>
> ##### axios.put(url[, data[, config]])
>
> ##### axios.patch(url[, data[, config]])

------

# 各种不同写法

:class

## **props**

##### 数组

```js
['todos']
```



##### 对象

```js
{todos:Array}
```



##### 对象

```js
{todos:{ type:Array,validato:(value) => {
    return value > 100
}}}
```





## **mapstate**







------

# localStorage 与 sessionStorage

#### localStorage 

访问了当前域名下的本地 [`Storage`](https://developer.mozilla.org/zh-CN/docs/Web/API/Storage) 对象，并通过 [`Storage.setItem()`](https://developer.mozilla.org/zh-CN/docs/Web/API/Storage/setItem) 增加了一个数据项目。

```js
localStorage.setItem('myCat', 'Tom');
```



该语法用于读取 `localStorage` 项，如下:

```js
let cat = localStorage.getItem('myCat');
```



该语法用于移除 `localStorage` 项，如下:

```js
localStorage.removeItem('myCat');
```



该语法用于移除所有的 `localStorage` 项，如下:

```js
// 移除所有
localStorage.clear();
```





#### sessionStorage

```js
// 保存数据到 sessionStorage
sessionStorage.setItem('key', 'value');

// 从 sessionStorage 获取数据
let data = sessionStorage.getItem('key');

// 从 sessionStorage 删除保存的数据
sessionStorage.removeItem('key');

// 从 sessionStorage 删除所有保存的数据
sessionStorage.clear();
```



------

# 商品详情页跳转的详细思路

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

# vuex中的actions中的commit

Vuex 中 使用 Action 处理异步请求时，常规写法如下：

```js
   getMenuAction:(context) =>{
            context.commit('SET_MENU_LIST',['承保2','核保2'])
        }
    }
```



我们也可以使用如下简化写法，如下：

```js
   actions:{
        getMenuAction:({commit}) =>{
            commit('SET_MENU_LIST',['承保2','核保2'])
        }
    }
```



我们可以看看  Vuex 中 注册 Action的源码如下：

![](H:\前端typora笔记\typora-user-images\23713642-63254481e6ddfba7.png)



例如变量常规写法:

```js
context = {  dispatch: local.dispatch,
　　　　　  commit: local.commit,
　　　　　  getters: local.getters,
　　　　　  state: local.state,
　　　　　  rootGetters: store.getters,
　　　　　  rootState: store.state
 }
```



##### **注：**

> **使用变量解构赋值后{ commit }的commit = context.commit 了**



------

# 路由导航守卫的理解（参考官网去写代码）

​		当路由跳转的时候，这个守卫可以去拦住，检测你是否有去往这个页面的条件
​		有特定条件才能去到相应的页面的功能  
​		拦截路由，查看是否满足条件，满足的放行，不满足的处理


```js
全局导航守卫： 
	无论是从哪个页面往哪个页面跳转，只要有路由跳转，就会拦住，进行检测
```

```js
	***前置守卫   配置的比较靠前       匹配路由前拦截，用的最多     
	解析守卫   配置的位置中间       匹配路由中拦截，用的比较少
	后置守卫   配置的比较靠后       匹配路由完成拦截，用的比较少
```


```js
***路由独享守卫：

	只能去拦住固定的往某个页面跳转的，是配置在当前路由当中，时间比较靠前
```


```js
组件内守卫：	
	只能去拦住固定的往某个页面跳转的，是配置在组件内部，也就是路由匹配已经完成了，时间比较靠后

	就是解析完了，已经跳转到组件的时候，但是组件还没创建成功的时候拦截
```



------

计算属性：我没有，但是我需要用，拿已有的数据计算生成我需要的数据

监视属性：我有，





------

# element-ui当中包含两大类组件

1. 直接写标签使用的组件，如button。引入后通过`Vue.component`或`Vue.use`去注册即可写标签使用

   ```js
   import { Button, Select } from 'element-ui';
   
   Vue.component(Button.name, Button);
   Vue.component(Select.name, Select);
   
   或写为:
   
   Vue.use(Button)
   Vue.use(Select)
   
   ```

   

   

2. > - 不能直接写标签的，本质上不是一个组件，而是一个对象或者函数。如`MessageBox`，它不能使用`Vue.component`或`Vue.use`去注册。
   >
   > - 通过挂载在Vue的原型上，以后在每个组件中都可以调用拿到这个对象
   >
   >   ```js
   >   Vue.prototype.$msgbox = MessageBox;
   >   Vue.prototype.$alert = MessageBox.alert;
   >   Vue.prototype.$confirm = MessageBox.confirm;
   >   Vue.prototype.$prompt = MessageBox.prompt;
   >                         
   >   Vue.prototype.$message = Message;
   >   ```
   >
   > 



------

# 图片懒加载特点说明

> (1)   还没有加载得到目标图片时, 先显示loading图片
>
> (2)   在<img>进入可视范围才加载请求目标图片



------













